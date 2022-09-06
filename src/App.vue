<template>
  <main>
    <Lift></Lift>
  </main>
</template>

<script>
import Lift from './components/Lift.vue';
  export default {
    data() {
        return {
            bottom: 0
        };
    },
    components: { Lift }
}
</script>

<style lang="sass">

</style>
