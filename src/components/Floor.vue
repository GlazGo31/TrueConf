<template>
  <div class="floor">

    <div class="content">
      <div>
        <span>{{ floorId }}</span>
      </div>
      <button class="floor__call"
              @click="callLift"
      >
        <span>Вызвать лифт</span>
      </button>
    </div>

  </div>
</template>

<script>
export default {
  props: {
    floorId: {
      type: Number,
      require: true,
    },
    assignedLift: {
      type: Number,
      required: false,
    }
  },
  methods: {
    callLift() {
      return this.$emit('callLift', this.floorId)
    }
  },

}
</script>

<style scoped>
.floor {
  height: 100px;
  width: 100%;
  border: 2px solid green;
  display: flex;
  justify-content: flex-start;
  align-items: center;
  font-size: 18px;
  color: white;
  padding: 10px;
}

</style>