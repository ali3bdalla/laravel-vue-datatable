<template>
  <div class="flex items-center justify-center gap-2">
    <inertia-link class="datagrid__actions-link" :href="`${endpoint}/${item.generated_number}`"
      ><i class="el-icon-view"></i> {{$page.props.layout_lang.common.show}}</inertia-link
    >
    <inertia-link class="datagrid__actions-link" :href="`${endpoint}/${item.generated_number}/edit`"
      ><i class="el-icon-edit"></i> {{ $page.props.layout_lang.common.edit }}</inertia-link
    >
  </div>
</template>
<script>
export default {
  props: {
    endpoint: {
      type: String
    },
    item: {
      type: Object,
      required: true
    }
  }

}
</script>
