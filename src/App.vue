<template>
  <div class="root">
    <div class="elevator-block"
         :style="{'grid-template-columns': `repeat(${appData.elevators.length}, 1fr)`}"
    >
      <v-elevator
          v-for="(elevator) in appData.elevators"
          :key="elevator"
          :currentStage="appData.selectedStages"
          :directionArrow="appData.beforeStage"
      />
    </div>

    <div class="stage-block"
         :style="{'grid-template-rows': `repeat(${appData.stages.length}, auto)`}"
    >
      <v-stage
          v-for="stage in appData.stages"
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
        elevators: [1], //количество лифтов
        stages: [], // количество этажей
        selectedStages: [],
        beforeStage: 1,
        elevatorIsReady: true,
        animatedElevator: false,
        nextStage: 0
      }
    }
  },
  methods: {
    clickButtonId(value) {

      if (value !== this.appData.selectedStages[0] && !this.appData.selectedStages.includes(value)) {
        if(value === this.appData.beforeStage) {
          return
        }
        this.appData.selectedStages.push(value)

        localStorage.setItem('data', JSON.stringify(this.appData))
        this.changeAppData('animatedElevator', true)

        this.moveElevator()
      }
    },

    moveElevator(pageUpdate = false) {


      if (this.appData.selectedStages.length === 0) {
        this.changeAppData('nextStage', 0)
      }

      if (this.appData.selectedStages.length > 0 && this.appData.elevatorIsReady) {

        this.changeAppData('elevatorIsReady', false)

        this.changeAppData('nextStage', this.appData.selectedStages[0])



        this.animationElevator(pageUpdate)
      }
    },
    animationElevator(pageUpdate) {
      gsap.to('.elevator-item', {
        bottom: this.getDistanceElevator(),
        duration: pageUpdate ? this.appData.nextStage - 1 : this.getSpeedElevator()
      }).then(() => {
        gsap.to('.elevator-item', {
          keyframes: [
            {opacity: 0},
            {opacity: 1, repeat: 2, duration: 1.5},
          ],
          ease: 'sine.out'
        }).then(() => {

          this.changeAppData('beforeStage', this.appData.selectedStages.shift())
          this.changeAppData('elevatorIsReady', true)

          this.moveElevator()

        })

      })
    },
    getSpeedElevator() {
      if (this.appData.animatedElevator) {
        if (this.appData.nextStage === this.appData.beforeStage) {
          return this.appData.beforeStage - 1
        }
        return this.appData.beforeStage > this.appData.nextStage ?
            this.appData.beforeStage - this.appData.nextStage : this.appData.nextStage - this.appData.beforeStage
      }
    },
    getDistanceElevator() {
      return this.appData.nextStage > 1 ? (this.appData.nextStage - 1) * 140 : 0
    },

    changeAppData(property, value) {
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
    initialStages(countStages) {
      const stagesLocal = []

      for (let i = 1; i <= countStages; i++) {
        stagesLocal.push(i)
      }
      this.appData.stages = stagesLocal.reverse()
      localStorage.setItem("data", JSON.stringify(this.appData))
    },
  },
  created() {
    this.updateLocalStorage()
    this.initialStages(6) //масштабируем количество этажей
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
