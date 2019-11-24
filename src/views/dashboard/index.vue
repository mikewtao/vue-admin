<template>
    <div class="title"> {{ name }}</div>

</template>

<script>
import { mapGetters } from 'vuex'

export default {
  name: 'Dashboard',
  computed: {
    ...mapGetters([
      'name'
    ])
  }
}
</script>

<style lang="scss" scoped>
.title {
    margin-left: 50px;
    font-size: 30px;
    line-height: 46px
}
</style>
