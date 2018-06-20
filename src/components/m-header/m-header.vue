<template>
  <div class="m-header">
      <div class="icon"></div>
      <h1 class="title">Chicken Music</h1>
  </div>
</template>

<script type="text/ecmascript-6">
  export default {
    
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "~common/stylus/variable"
  @import "~common/stylus/mixin"

  .m-header
    position: relative
    height: 44px
    text-align: center
    font-size: 0
    color: $color-theme
    .icon
      display: inline-block
      vertical-align: middle
      width: 30px
      height: 32px
      bg-image('logo')
      background-size: 30px 32px
      margin-right: 8px
    .title
      display: inline-block
      vertical-align: middle
      line-height: 44px
      font-size: $font-size-large

</style>