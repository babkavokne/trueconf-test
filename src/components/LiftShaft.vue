<template>
  <div class="well">
    <div class="lift" :id="this.liftInfo.id">
      <div class="arrow">asd</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
    }
  },
  props: {
    info: Object,
    liftInfo: Object
  },
  methods: {
  },
  watch: {
    'liftInfo.status'(newValue) {
      this.$emit('statusChanged', newValue)
    }
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
    // transition: all 1s
    .arrow
      position: absolute
      top: -25px
      left: 50%
      transform: translateX(-50%)
</style>