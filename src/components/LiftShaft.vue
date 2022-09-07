<template>
  <div class="well">
    <div :class="this.liftInfo.status === 'pending' ? 'lift pending' : 'lift'" :id="this.liftInfo.id">
      <div class="arrow" v-if="this.liftInfo.goesTo">
        {{ this.liftInfo.direction === 'up' ? '↑' : '↓' }}
        {{ this.liftInfo.goesTo }}</div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    info: Object,
    liftInfo: Object
  },
  mounted() {
    const well = document.querySelectorAll('.well');
    well.forEach(el => el.style.height = this.info.floorHeight * this.info.floors + 'px');
  }
}
</script>

<style lang="sass" scoped>
.well 
  height: 150px
  min-width: 104px
  border-left: 2px solid black
  border-right: 2px solid black
  position: absolute
  .lift
    position: absolute
    height: 150px
    width: 100px
    background-color: #fff
    bottom: 0
    &.pending
      animation: flicker 1s infinite
    .arrow
      position: absolute
      padding: 4px
      background-color: green
      min-width: 30px
      display: flex
      justify-content: center
      color: #fff
      top: 10px
      left: 50%
      transform: translateX(-50%)

@keyframes flicker 
  0%, 100% 
    opacity: 1
    background-color: #fff
  50%         
    opacity: .5
    background-color: red
 
</style>