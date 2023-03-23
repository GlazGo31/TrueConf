<template>
  <div class="elevator">
    <div class="elevator-item">
      <div class="elevator-info">
        <div class="elevator__direction">

          {{ currentStage[0] < directionArrow ? '&darr;' : '&uarr;' }}

        </div>
        <div class="elevator__number-stage">

          {{ currentStage[0] }}

        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "v-Elevator",
  props: {
    currentStage: {
      type: Array,
      default() {
        return [];
      },
    },

    directionArrow: {
      type: Number,
      required: true
    }
  },
  computed: {}
}
</script>

<style>
.elevator {
  display: grid;
  border: 2px solid black;
  width: 200px;
  height: 100%;
  padding: 0 10px;
  background-color: #a7d0d0;
  position: relative;
}

.elevator-item {
  width: 100%;
  height: 130px;
  background-color: #777777;
  position: absolute;
  bottom: 0;
}

.elevator-info {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 50%;
  height: 60px;
  background-color: #360707;
  color: white;
  font-size: 30px;
  font-weight: bold;
  margin: 0 auto;
}
</style>