<template>
<!--  <el-select-->
<!--    size="large"-->
<!--    v-model="createdBy"-->
<!--    @change="changed"-->
<!--    filterable-->
<!--    :placeholder="$page.props.layout_lang.common.created_by"-->
<!--  >-->
<!--    <el-option-->
<!--      v-for="(item,index) in createdByUsers"-->
<!--      :key="index"-->
<!--      :label="item.name"-->
<!--      :value="item.id"-->
<!--    >-->
<!--    </el-option>-->
<!--  </el-select>-->
</template>

<script>
export default {
  props: {
    value: {
      // type:Object
    }
  },
  data () {
    return {
      createdByUsers: [{ name: 'All' }],
      createdBy: null
    }
  },
  created () {
    this.createdBy = this.value && this.value > 0 ? this.value : ''
    this.$page.props.organization_managers.forEach(element => {
      this.createdByUsers.push(element)
    })
  },
  methods: {
    changed () {
      this.$emit('input', this.createdBy)
      this.$emit('change', this.createdBy)
    }
  }
}
</script>

<style>
</style>
