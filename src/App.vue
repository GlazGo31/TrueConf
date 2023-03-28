<template>
  <div class="root">
    <div class="elevator-block"
         :style="{'grid-template-columns': `repeat(${appData.countElevators.length}, 1fr)`}"
    >
      <v-elevator
          v-for="(elevator) in appData.countElevators"
          :key="elevator"
          :currentStage="appData.selectedStages"
          :directionArrow="appData.beforeStage"
      />
    </div>

    <div class="stage-block"
         :style="{'grid-template-rows': `repeat(${appData.countStages.length}, auto)`}"
    >
      <v-stage
          v-for="stage in appData.countStages"
          :key="stage"
          :stage="stage"
          :current-stage="appData.selectedStages"
          @clickButtonId="clickButtonId"
      />
    </div>
  </div>
</template>

<script>
import VElevator from "@/components/v-Elevator.vue";
import VStage from "@/components/v-Stage.vue";
import {gsap} from "gsap";

export default {
  name: 'App',
  components: {VStage, VElevator},
  data() {
    return {
      appData: {
        countElevators: [], //количество лифтов
        countStages: [], // количество этажей
        lineElevators: [], // очередь лифтов
        selectedStages: [],
        beforeStage: 1,
        elevatorIsReady: true,
        animationElevator: false,
        nextStage: 0
      }
    }
  },
  methods: {
    clickButtonId(value) {
      if (value !== this.appData.selectedStages[0] && value !== this.appData.beforeStage || this.appData.selectedStages.length > 0) {
        this.appData.selectedStages.push(value)

        localStorage.setItem('data', JSON.stringify(this.appData))
        this.modificationAppData('animationElevator', true)
          this.moveElevator()



      }
      if(this.appData.nextStage !== 0 || this.appData.selectedStages.length === 0 || value === this.appData.beforeStage  ) {
        return
      }
    },

    moveElevator(pageUpdate = false) {
      if (this.appData.selectedStages.length === 0) {
        this.modificationAppData('nextStage', 0)
      }

      if (this.appData.selectedStages.length > 0 && this.appData.elevatorIsReady) {

        this.modificationAppData('elevatorIsReady', false)

        this.modificationAppData('nextStage', this.appData.selectedStages[0])



        this.animatedElevator(pageUpdate)
      }
    },
    animatedElevator(pageUpdate) {
      gsap.to('.elevator-item', {
        bottom: this.defaultDistanceElevator(),
        duration: pageUpdate ? this.appData.nextStage - 1 : this.defaultSpeedElevator()
      }).then(() => {

        gsap.to('.elevator-item', {
          keyframes: [
            {opacity: 0},
            {opacity: 1, repeat: 2, duration: 1.5},
          ],
          ease: 'sine.out'
        }).then(() => {

          this.modificationAppData('beforeStage', this.appData.selectedStages.shift())
          this.modificationAppData('elevatorIsReady', true)

          this.moveElevator(false)

        })

      })
    },
    defaultSpeedElevator() {
      if (this.appData.animationElevator) {
        if (this.appData.nextStage === this.appData.beforeStage) {
          return this.appData.beforeStage - 1
        }
        return this.appData.beforeStage > this.appData.nextStage ?
            this.appData.beforeStage - this.appData.nextStage : this.appData.nextStage - this.appData.beforeStage
      }
    },
    defaultDistanceElevator() {
      return this.appData.nextStage > 1 ? (this.appData.nextStage - 1) * 140 : 0
    },

    modificationAppData(property, value) {
      this.appData[property] = value
      localStorage.setItem('data', JSON.stringify(this.appData))
    },

    updateLocalStorage() {
      const localStorageData = localStorage.getItem("data")

      if (localStorageData) {
        this.appData = {...JSON.parse(localStorageData)}
      } else {

        localStorage.setItem("data", JSON.stringify(this.appData))
        }

    },
    initializationStages(count) {
      const primaryStages = []

      for (let i = 1; i <= count; i++) {
        primaryStages.push(i)
      }
      this.appData.countStages = primaryStages.reverse()
      localStorage.setItem("data", JSON.stringify(this.appData))
    },

    initializationElevators(count) {
      const primaryElevators = []

      for (let i = 1; i <= count; i++) {
        primaryElevators.push({id: i, visitedFloor: null})
      }
      this.appData.countElevators = [...primaryElevators]
      console.log(this.appData.countElevators)
      localStorage.setItem("data", JSON.stringify(this.appData))
    },
  },
  created() {
    this.updateLocalStorage()
    this.initializationStages(6) //масштабируем количество этажей
    this.initializationElevators(3) // масшатабируем количество лифтов

  },
  mounted() {
    this.appData.elevatorIsReady = true
    this.moveElevator(true)
  }
}
</script>

<style lang="scss">
.root {
  width: 100%;
  height: 100%;
  display: flex;
  gap: 20px;

  .elevator-block {
    display: grid;
    column-gap: 20px;
  }

  .stage-block {
    width: 100%;
    display: grid;
  }
}
</style>
