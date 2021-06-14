<template>
    <div
        class="datagrid"
    >
        <div
            class="datagrid__header"
        >
            <div class="datagrid__header-title-container">
                <h3 class="datagrid__header-title">
                    <slot name="title">
                        <i class="el-icon-bank-card"/> Data
                    </slot>
                    ({{
                        tableData.meta ? tableData.meta.total : ''
                    }})
                </h3>
                <div class="datagrid__header-actions-container">
                    <slot name="header_actions">
                        <inertia-link
                            v-if="hasCreateButton"
                            :href="getPath + '/create'"
                        >
                            <el-button size="large" type="primary">
                                {{ $page.props.layout_lang.common.create }}
                            </el-button>
                        </inertia-link>
                    </slot>
                </div>
            </div>
            <div class="datagrid__header-filters-container">
                <sort-by-filter
                    v-model="tableState.sorted_by"
                    :addtional-sorded-by-items="addtionalSordedByItems"
                    :value="tableState.sorted_by"
                    @change="resetPagination"
                />
                <slot name="additional_header_filters"/>
                <created-at-filter
                    v-model="tableState.created_at"
                    :value="tableState.created_at"
                    @change="resetPagination"
                />
                <created-by-filter
                    v-if="hasCreatedBy"
                    v-model="tableState.created_by"
                    :value="tableState.created_by"
                    @change="resetPagination"
                />
                <slot name="header_search">
                    <el-input
                        v-model="tableState.searchTxt"
                        :placeholder="$page.props.layout_lang.common.type_somthing_for_search"
                        size="large"
                        @change="resetPagination"
                    />
                </slot>
            </div>
        </div>
        <div v-loading="fetching"
             element-loading-background="rgb(255, 255, 255)"
             element-loading-spinner="el-icon-loading"
             element-loading-text=""
        >
            <el-table

                :data="tableData.data"
                :height="tableHeight"
                :max-height="tableHeight"
                border
                size="medium"
                sortable
                stripe
                style="width: 100%"
            >
                <slot v-bind="{ tableState }" name="tableView"/>
                <el-table-column
                    v-if="hasCreatedBy"
                    :label="$page.props.layout_lang.common.created_by"
                    align="center"
                    header-align="center"
                    prop="created_by.name"
                    width="250"
                >
                    <template slot-scope="{ row, column, $index }">
                        <CreatedBy :item="row"/>
                    </template>
                </el-table-column>
                <el-table-column
                    :label="$page.props.layout_lang.common.date_and_time"
                    align="center"
                    header-align="center"
                    prop="created_at"
                    width="180"
                >
                    <template slot-scope="{ row, column, $index }">
                        <CreatedAt :item="row"/>
                    </template>
                </el-table-column>
                <slot name="actions">
                    <el-table-column
                        :label="$page.props.layout_lang.common.options"
                        align="center"
                        width="150"
                    >
                        <template slot-scope="{ row, column, $index }">
                            <slot>
                                <Actions :endpoint="getPath" :item="row"/>
                            </slot>
                        </template>
                    </el-table-column>
                </slot>
            </el-table>
        </div>
        <div>
            <paginate
                :init-page="tableState.currentPage"
                :table-data="tableData"
                @changed="pageChanged"
            />
        </div>
    </div>
</template>

<script>
import CreatedBy from './CreatedBy'
import CreatedAt from './CreatedAt'
import RowId from './RowId'
import Actions from './Actions'
import Paginate from './Paginate'
import AlertMixin from './../../../Mixins/AlertMixin'
import CreatedByFilter from './CreatedByFilter.vue'
import CreatedAtFilter from './CreatedAtFilter.vue'
import SortByFilter from './sortByFilter.vue'

export default {
    components: {
        CreatedBy,
        CreatedAt,
        Actions,
        Paginate,
        RowId,
        CreatedByFilter,
        CreatedAtFilter,
        SortByFilter
    },
    mixins: [AlertMixin],
    props: {
        hasCreateButton: {
            type: Boolean,
            default: true
        },
        filters: {
            type: Object
        },
        hasCreatedBy: {
            type: Boolean,
            default: false
        },
        endpoint: {
            type: String,
            required: true
        },
        actionsLink: {
            type: String
        },
        addtionalSordedByItems: {
            type: Array
        }
    },
    data () {
        return {
            tableHeight: window.innerHeight - 300,
            fetching: true,
            tableState: {
                activeUi: 'table',
                searchTxt: '',
                created_at: {},
                created_by: 0,
                sorted_by: null,
                currentPage: 1
            },
            tableData: {}
        }
    },
    computed: {
        getPath () {
            return this.actionsLink ?? window.location
        },
        getTableParams () {
            return {
                page: this.tableState.currentPage,
                search: this.tableState.searchTxt,
                sort_by: JSON.stringify(this.tableState.sorted_by),
                created_at: JSON.stringify(this.tableState.created_at),
                created_by: this.tableState.created_by,
                filters: JSON.stringify(this.tableState.filters)
            }
        }
    },
    watch: {
        filters: {
            deep: true,
            handler (value, oldValue) {
                this.tableState.filters = value
                this.saveTableState()
            }
        },
        tableState: {
            deep: true,
            handler (value, oldValue) {
                this.saveTableState()
                this.fetch()
            }
        }
    },
    created () {
        this.resetTableState()
    },

    methods: {
        changeActiveUi (ui) {
            this.tableState.activeUi = ui
            this.saveTableState()
        },
        resetPagination () {
            this.tableState.currentPage = 1
            this.fetch()
        },
        tableStateKey () {
            return `table_${this.endpoint}_${this.$page.props.loggedManager.id}`
        },
        saveTableState () {
            localStorage.setItem(
                this.tableStateKey(),
                JSON.stringify(this.tableState)
            )

            this.$emit('tableStateChanged', this.getTableParams)
        },
        resetTableState () {
            const state = localStorage.getItem(this.tableStateKey())

            if (state) this.tableState = JSON.parse(state)
            this.$emit('tableStateHasBeenReset', this.tableState)
            this.fetch()
        },
        pageChanged (e) {
            this.tableState.currentPage = e.page
            this.fetch()
        },
        fetch () {
            this.fetching = true
            axios
                .get(this.endpoint, {
                    params: this.getTableParams
                })
                .then((res) => {
                    this.tableData = res.data
                })
                .catch((error) => {
                    this.alertUser('Server Error', `${error.message}`, 'warning')
                })
                .finally(() => {
                    this.fetching = false
                    this.saveTableState()
                })
        }
    }
}
</script>
