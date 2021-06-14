<template>
  <div>{{ item.created_at }}</div>
<!--  new Date(-->
</template>
<script>
export default {
  props: {
    item: {
      type: Object,
      required: true
    }
  }
}
</script>
