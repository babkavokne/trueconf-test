<template>
  <main>
    <LiftShaft :info="info" :liftInfo="lifts[lift.id]" @statusChanged="status" v-for="lift in lifts" />
    <div class="buttons">
      <div class="btn" v-for="btn in info.floors">
        <button @click="call" :id="btn">
          Вызов
        </button>
      </div>
    </div>
  </main>
</template>

<script>
import LiftShaft from "./LiftShaft.vue";
export default {
  data() {
    return {
      info: {
        floors: 5,
        liftsAmount: 1,
        floorHeight: 150,
      },
      lifts: [],
      callStack: []
    };
  },
  methods: {
    call(e) {
      const isCalledFromFloor = this.callStack.find(el => el.callFromFloor === e.target.id);
      if (!isCalledFromFloor) {
        this.callStack.push({ callFromFloor: e.target.id });
        this.callHandler();
      }
    },
    callHandler() {
      // console.log(this.callStack.shift(), this.callStack);
      // while (!this.callStack.length) { // Временно всегда положителен
        const liftsCabins = Array.from(document.querySelectorAll(".lift"))
        const liftsCabin = liftsCabins.find((el, i) => this.lifts[i].status === 'free');
        const lift = this.lifts[liftsCabin.id];

        lift.status = 'busy';
        lift.calledFromFloor = this.callStack.shift().callFromFloor; 

        // { id: i, onFloor: 1, status: 'free', calledFromFloor: null }
        const floorHeight = this.info.floorHeight;
        const id = setInterval(() => {

          if (lift.calledFromFloor >= lift.onFloor) {
            liftsCabin.style.bottom = +liftsCabin.style.bottom.slice(0, -2) + floorHeight / 100 + "px";

            if (+liftsCabin.style.bottom.slice(0, -2) > floorHeight * (lift.calledFromFloor - 1)) {
              lift.onFloor = lift.calledFromFloor;
              liftsCabin.style.bottom = floorHeight * (lift.calledFromFloor - 1) + 'px';
              lift.status = 'pending';
              console.log(lift.status, lift.onFloor);
              setTimeout(() => {
                lift.status = 'free';
                console.log('free');
              }, 3000);
              clearInterval(id);
            }
          }

          if (lift.calledFromFloor < lift.onFloor) {
            liftsCabin.style.bottom = +liftsCabin.style.bottom.slice(0, -2) - floorHeight / 100 + "px";

            if (+liftsCabin.style.bottom.slice(0, -2) < floorHeight * (lift.calledFromFloor - 1)) {
              lift.onFloor = lift.calledFromFloor;
              liftsCabin.style.bottom = floorHeight * (lift.calledFromFloor - 1) + 'px';
              lift.status = 'pending';
              console.log(lift.status, lift.onFloor);
              setTimeout(() => {
                lift.status = 'free';
                console.log('free');
              }, 3000);
              clearInterval(id);
            }
          }

        }, 10);
      }
    // }
  },
  beforeMount() {
    const liftsAmount = this.info.liftsAmount;
    for (let i = 0; i < liftsAmount; i++) {
      this.lifts.push({ id: i, onFloor: 1, status: 'free', calledFromFloor: null })
    }
  },
  mounted() {
    const wells = document.querySelectorAll('.well');
    const buttons = document.querySelectorAll('.btn');
    const height = this.info.floorHeight;
    const liftsAmount = this.info.liftsAmount;
    wells.forEach((el, i) => el.style.left = 20 * (i + 1) + 100 * i + 'px');
    buttons.forEach(el => el.style.padding = `${height / 2 - 9.7}px 0 ${height / 2 - 9.7}px ${liftsAmount * 104 + liftsAmount * 24}px`);
  },
  components: { LiftShaft }
}
</script>

<style lang="sass" scoped>
main
  min-height: 100vh
  height: 100%
  display: flex
  align-items: end
  .buttons
    display: flex
    flex-direction: column-reverse
    div
      width: 100vw
      border-top: 1px solid #fff
      button
        border: none
        cursor: pointer
    

</style>