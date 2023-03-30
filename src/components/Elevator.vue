<template>
  <div class="elevator-shaft">

    <div class="elevator"
         :class="`elevator-id-${elevatorId}`"
    >
      <div class="content">
        <span>{{ elevatorId }}</span>
        <span>{{ floorDestination < floorDestination ? '&darr;' : '&uarr;' }}</span>
      </div>
    </div>

  </div>

</template>

<script>
export default {
  props: {
    elevatorId: {
      type: Number,
      required: true,
    },
    floorDestination: {
      type: Number,
      required: true,
    },
  },
}
</script>

<style scoped>
.elevator-shaft {
  height: 100%;
  position: relative;
  width: 100px;
}
.elevator {
  position: absolute;
  left: 0;
  bottom: 0;
  background: #300699;
  width: 100%;
  height: 100px;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 20px;
  color: white;
}
</style>