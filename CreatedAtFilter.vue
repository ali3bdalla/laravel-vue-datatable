<template>
<!--  <el-date-picker-->
<!--      v-model="dates"-->
<!--      :default-value="defaultValue"-->
<!--      :end-placeholder="$page.props.layout_lang.common.end_at"-->
<!--      :start-placeholder="$page.props.layout_lang.common.start_at"-->
<!--      range-separator="-"-->
<!--      size="large"-->
<!--      type="daterange"-->
<!--      @change="changed"-->
<!--  >-->
<!--  </el-date-picker>-->
</template>

<script>
export default {
  data () {
    return {
      dates: {},
      defaultValue: null
    }
  },
  props: {
    value: {
      type: Object
    }
  },
  created () {
    if (this.value && this.value.start_at && this.value.end_at) {
      this.defaultValue = [new Date(this.value.start_at), new Date(this.value.end_at)]
      this.dates = this.defaultValue
    }
  },
  methods: {
    changed () {
      const startDate = this.dates ? this.dates[0] : ''
      const endDate = this.dates ? this.dates[1] : ''
      this.$emit('input', {
        start_at: startDate,
        end_at: endDate
      })
      this.$emit('change', {
        start_at: startDate,
        end_at: endDate
      })
    }
  }
}
</script>

<style>
</style>
