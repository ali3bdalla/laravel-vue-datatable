<template>
<!--    <el-select v-model="sortedBy" size="large" filterable :placeholder="$page.props.layout_lang.common.sort_by"-->
<!--               @change="changed"-->
<!--    >-->
<!--        <el-option-->
<!--            v-for="(item,index) in sortedByList"-->
<!--            :key="index"-->
<!--            :label="item.label"-->
<!--            :value="index"-->
<!--        />-->
<!--    </el-select>-->
</template>

<script>
export default {
    props: {
        value: {
            default: () => {},
            type: [Object,String,Number]
        },
        additionalSortedByItems: {
            type: Array,
            default: () => []
        }
    },
    data () {
        return {
            sortedByList: [
                // { key: 'id', direction: 'asc', label: this.$page.props.layout_lang.common.sort.oldest },
                // { key: 'id', direction: 'desc', label: this.$page.props.layout_lang.common.sort.newest }
            ],
            sortedBy: null
        }
    },
    created () {
        Array.from(this.additionalSortedByItems).forEach(item => {
            if (item.key && item.direction && item.label) {
                this.sortedByList.push(item)
            }
        })
    },
    methods: {
        changed (e) {
            this.$emit('input', this.sortedByList[this.sortedBy])
            this.$emit('change', this.sortedByList[this.sortedBy])
        }
    }
}
</script>

<style>
</style>
