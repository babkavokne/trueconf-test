<template>
  <main>
    <LiftShaft :info="info" :liftInfo="lifts[lift.id]" v-for="lift in lifts" />
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
        floors: 15,
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
      const liftOnFloor = this.lifts.find(el => el.onFloor == e.target.id && el.status === 'free');
      const goesTo = this.lifts.find(el => +el.goesTo === +e.target.id);

      if (!isCalledFromFloor && !liftOnFloor && !goesTo) {
        this.callStack.push({ callFromFloor: e.target.id });
        localStorage.setItem('callStack', JSON.stringify(this.callStack))
        e.target.style.color = 'red';
        if (this.callStack.length === 1) this.callHandler();
      }
      console.log(this.callStack.length);
    },
    callHandler() {
      const btns = Array.from(document.querySelectorAll("button"))
      const liftsCabins = Array.from(document.querySelectorAll(".lift"))
      const liftsCabin = liftsCabins.find((el, i) => this.lifts[i].status === 'free');
      const lift = this.lifts[liftsCabin.id];
      const floorHeight = this.info.floorHeight;

      lift.status = 'busy';
      lift.calledFromFloor = this.callStack[0].callFromFloor;
      lift.goesTo = lift.calledFromFloor;

      const btn = btns[lift.calledFromFloor - 1];

      if (+lift.calledFromFloor >= +lift.onFloor) {

        lift.direction = '↑';

        const id = setInterval(() => {

          liftsCabin.style.bottom = +liftsCabin.style.bottom.slice(0, -2) + floorHeight / 100 + "px";
          localStorage.setItem('position', +liftsCabin.style.bottom.slice(0, -2))

          if (
            +liftsCabin.style.bottom.slice(0, -2) >= floorHeight * (lift.calledFromFloor - 1)
          ) {
            lift.onFloor = +lift.calledFromFloor;
            liftsCabin.style.bottom = floorHeight * (lift.calledFromFloor - 1) + 'px';
            localStorage.setItem('position', +liftsCabin.style.bottom.slice(0, -2));
            lift.status = 'pending';

            clearInterval(id);

            setTimeout(() => {
              lift.status = 'free';
              btn.style.color = 'black';
              lift.goesTo = null;
              lift.direction = null;

              this.callStack.shift();
              localStorage.setItem('callStack', JSON.stringify(this.callStack));
              localStorage.setItem('lifts', JSON.stringify(this.lifts));

              if (this.callStack.length) this.callHandler();
            }, 3000);

          }
        }, 1)
      } else {

        lift.direction = '↓';

        const id = setInterval(() => {

          liftsCabin.style.bottom = +liftsCabin.style.bottom.slice(0, -2) - floorHeight / 100 + "px";
          localStorage.setItem('position', +liftsCabin.style.bottom.slice(0, -2));

          if (
            +liftsCabin.style.bottom.slice(0, -2) < floorHeight * (lift.calledFromFloor - 1)
          ) {
            lift.onFloor = +lift.calledFromFloor;
            liftsCabin.style.bottom = floorHeight * (lift.calledFromFloor - 1) + 'px';
            localStorage.setItem('position', +liftsCabin.style.bottom.slice(0, -2));
            lift.status = 'pending';

            clearInterval(id);

            setTimeout(() => {
              lift.status = 'free';
              btn.style.color = 'black';
              lift.goesTo = null;
              lift.direction = null;

              this.callStack.shift();
              localStorage.setItem('callStack', JSON.stringify(this.callStack));
              localStorage.setItem('lifts', JSON.stringify(this.lifts));

              if (this.callStack.length) this.callHandler();
            }, 3000);
          }
        }, 1)
      }
    }
  },
  beforeMount() {
    const liftsAmount = this.info.liftsAmount;

    if (localStorage.getItem('lifts')) {
      this.lifts = JSON.parse(localStorage.getItem('lifts'));
    } else {
      for (let i = 0; i < liftsAmount; i++) {
        this.lifts.push(
          { id: i, onFloor: 1, status: 'free', calledFromFloor: null, goesTo: null, direction: null }
        )
      }
    }
  },
  mounted() {
    const wells = document.querySelectorAll('.well');
    const buttons = document.querySelectorAll('.btn');
    const floorHeight = this.info.floorHeight;
    const liftsAmount = this.info.liftsAmount;

    wells.forEach((el, i) => el.style.left = 20 * (i + 1) + 100 * i + 'px');
    buttons.forEach(el => el.style.padding = `${floorHeight / 2 - 9.7}px 0 ${floorHeight / 2 - 9.7}px ${liftsAmount * 115 + liftsAmount * 24}px`);

    if (JSON.parse(localStorage.getItem('callStack')) && localStorage.getItem('position')) {

      this.callStack = [...JSON.parse(localStorage.getItem('callStack'))];

      const liftsCabins = Array.from(document.querySelectorAll(".lift"));
      const liftsCabin = liftsCabins.find((el, i) => this.lifts[i].status === 'free');

      liftsCabin.style.bottom = localStorage.getItem('position') + 'px';

      if (this.callStack.length) {
        const btns = Array.from(document.querySelectorAll("button"));
        const btnsIndexes = this.callStack.map(el => el.callFromFloor)
        const filteredBtns = btns.filter((el, i) => btnsIndexes.includes(`${i + 1}`));
        console.log(filteredBtns);

        filteredBtns.forEach(el => el.style.color = 'red');

        this.callHandler();
      }
    }
  },
  components: { LiftShaft }
}
</script>

<style lang="sass" scoped>
main
  overflow-x: hidden 
  min-height: 100vh
  height: 100%
  display: flex
  align-items: end
  .buttons
    display: flex
    flex-direction: column-reverse
    .btn
      width: 100vw
      border-top: 1px solid #fff
      button
        border: none
        cursor: pointer
</style>