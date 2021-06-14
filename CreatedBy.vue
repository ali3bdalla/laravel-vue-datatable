<template>
  <div class="flex items-center justify-center gap-3" v-if="item.created_by">
<!--    <inertia-link href="#">{{ item.created_by.locale_name }}</inertia-link>-->
  </div>
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
