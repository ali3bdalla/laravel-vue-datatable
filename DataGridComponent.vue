<template>
    <div
    >
      <div class="bg-white shadow-md rounded">
        <el-table
                class="min-w-max w-full table-auto"
                :data="tableData.data"
                :height="tableHeight"
                :max-height="tableHeight"
                border
                highlight-current-row
                current-row-key
                :row-class-name="(row,rowIndex) => 'border-b border-gray-200 bg-gray-50 hover:bg-gray-100'"
                :header-row-class-name="(row,rowIndex) =>
                'bg-gray-200 text-gray-600 uppercase text-sm leading-normal bg-gray-200'"
                :header-cell-class-name="(row,rowIndex) =>
                'py-3 px-6 text-center'"
                :cell-class-name="(row, column, rowIndex, columnIndex) =>
                'py-3 px-6 text-left'"
                size="medium"
                tooltip-effect="dark"
                show-summary
                lazy
                sortable
                stripe
            >
                <slot v-bind="{ tableState }" />
<!--              :label="$page.props.layout_lang.common.created_by"-->
<!--                <el-table-column-->
<!--                    v-if="hasCreatedBy"-->

<!--                    align="center"-->
<!--                    header-align="center"-->
<!--                    prop="created_by.name"-->
<!--                    width="250"-->
<!--                >-->
<!--                    <template slot-scope="{ row, column, $index }">-->
<!--                        <CreatedBy :item="row"/>-->
<!--                    </template>-->
<!--                </el-table-column>-->
<!--              :label="$page.props.layout_lang.common.date_and_time"-->
<!--                <el-table-column-->
<!--                    align="center"-->
<!--                    header-align="center"-->
<!--                    prop="created_at"-->
<!--                    width="180"-->
<!--                >-->
<!--                    <template slot-scope="{ row, column, $index }">-->
<!--                        <CreatedAt :item="row"/>-->
<!--                    </template>-->
<!--                </el-table-column>-->
<!--                <slot name="actions">-->
<!--&lt;!&ndash;                  :label="$page.props.layout_lang.common.options"&ndash;&gt;-->
<!--                    <el-table-column-->

<!--                        align="center"-->
<!--                        width="150"-->
<!--                    >-->
<!--                        <template slot-scope="{ row, column, $index }">-->
<!--                            <slot>-->
<!--                                <Actions :endpoint="getPath" :item="row"/>-->
<!--                            </slot>-->
<!--                        </template>-->
<!--                    </el-table-column>-->
<!--                </slot>-->
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
        additionalSortedByItems: {
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
        resetPagination () {
            this.tableState.currentPage = 1
            this.fetch()
        },
        tableStateKey () {
          // _${this.$page.props.loggedManager.id}
            return `table_${this.endpoint}`
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
                    // this.alertUser('Server Error', `${error.message}`, 'warning')
                })
                .finally(() => {
                    this.fetching = false
                    this.saveTableState()
                })
        }
    }
}
</script>
