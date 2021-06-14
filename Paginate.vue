<template>
    <div class="mt-5 text-center">
        <el-pagination
            :current-page="initPage" background layout="prev, pager, next"
            :total="tableData.meta ? tableData.meta.last_page : 0"
            @current-change="currentPageChanged"
            @next-click="currentPageChanged" @prev-click="currentPageChanged"
        />
    </div>
</template>

<script>
export default {
    props: {
        initPage: {
            type: Number
        },
        tableData: {
            type: Object,
            required: true
        }
    },
    methods: {
        currentPageChanged (e) { this.$emit('changed', { page: e }) }
    }
}
</script>

<style>
</style>
