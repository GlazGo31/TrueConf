<template>
<div class="stage" ref="elementFill">
  <div
      class="stage-number"
  >
    {{stage}}
  </div>
  <button
      class="stage-btn"
      @click="changeDirectory"
      :disabled="this.currentStage.includes(stage)"
      :class="{ active: this.currentStage.includes(stage)}"
  >
    {{ this.currentStage.includes(stage) ? 'Ожидание': 'Вызвать' }}
  </button>
</div>
</template>

<script>
export default {
  name: "v-Stage",
  props: {
    stage: {
      type: Number,
      default() {
        return undefined
      }
    },
    currentStage: {
      type: Array,
      default() {
        return [];
      }
    }
  },
  computed: {

  },
  methods: {
    changeDirectory() {
      return this.$emit('clickButtonId', this.stage)
    }
  }
}
</script>

<style>
.stage {
  padding: 20px;
  display: flex;
  flex-flow: column nowrap;
  justify-content: center;
  gap: 20px;
  border-bottom: 1px solid black;
  border-top: 1px solid black;
}
.stage-number {
  font-size: 25px;
  font-weight: bold;
}

.stage-btn {
  align-self: flex-start;
  width: 100px;
  height: 50px;
  font-size: 18px;
  font-family: "Franklin Gothic Medium";
  font-weight: lighter;
  border-radius: 100px;
  cursor: pointer;
  background-color: transparent;
}

.stage-btn:hover {
  background-color: #f1f1f1;
}

.active  {
  background-color: #a7d0d0;
  animation: pulse 1s linear infinite;
}
@keyframes pulse {
  0% {
    transform: scale(1);
    opacity: 0.7;
  }
  50% {
    opacity: 1;
  }
  100% {
    transform: scale(1.2);
    opacity: 0.7;
  }
}
</style>