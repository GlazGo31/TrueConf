<template>
  <div class="app">
    <div class="container">
      <div class="elevators-container">
        <Elevator v-for="elevator in elevators"
                  :elevatorId="elevator.elevatorId"
                  :floorDestination="elevator.floorDestination"
        />
      </div>

      <div class="floors-container">
        <Floor v-for="floor in floors"
               :floorId="floor.floorId"
               @callLift="callLift"
        />
      </div>
    </div>

  </div>
</template>

<script>

import Elevator from "@/components/Elevator.vue";
import Floor from "@/components/Floor.vue";
import {gsap} from "gsap";

const elevatorExample = {
  elevatorId: 1,
  currentFloor: 1,
  floorDestination: 1,
}

const floorExample = {
  floorId: 1,
  isHasElevator: false,
}


export default {
  name: 'App',
  components: {
    Floor,
    Elevator,

  },
  data() {
    return {
      elevators: [],
      floors: [],
      readyElevators: [],
    }
  },

  methods: {

    calcDistanceToFloor(elevatorCurrentFloor, floorDestination) {
      if (elevatorCurrentFloor > floorDestination) {
        return elevatorCurrentFloor - floorDestination
      } else {
        return floorDestination - elevatorCurrentFloor
      }
    },


    callLift(floorDestination) { // ТОЧКА ВХОДА ПРИ ВЫЗОВЕ ЛИФТА.
      if (this.readyElevators.length === 0) return

      let elevatorIsAtFloor = false

      this.floors.forEach(floor => {
        if (floor.floorId == floorDestination) {

          if (floor.isHasElevator) {
            elevatorIsAtFloor = true
          }
        }
      })

      if (elevatorIsAtFloor) return

      // ОТсюда идёт логика движения лифтов, а выше просто проверка на наличие лифта на этаже.


      const mapClosestElevator = {} // Маппер.

      this.readyElevators.forEach(elevator => {
        mapClosestElevator[elevator.elevatorId] = this.calcDistanceToFloor(elevator.currentFloor, floorDestination)
      })

      const mapClosestElevatorKeys = Object.keys(mapClosestElevator)
      const mapClosestElevatorValues = Object.values(mapClosestElevator)

      let elevatorIndex = mapClosestElevatorValues.indexOf(Math.min(...mapClosestElevatorValues))

      let elevator

      this.readyElevators.forEach((elevatorItem, index, readyElevators) => {
        if (String(elevatorItem.elevatorId) === mapClosestElevatorKeys[elevatorIndex]) {
          elevator = elevatorItem
          readyElevators.splice(index, 1)
        }
      })

      elevator.floorDestination = floorDestination

      this.floors.forEach(floor => {
        if (floor.floorId == floorDestination) {
          floor.isHasElevator = true
        }
      })

      this.floors.forEach(floor => {
        if (floor.floorId == elevator.currentFloor) {
          floor.isHasElevator = false
        }
      })

      elevator.currentFloor = null
      this.moveElevator(elevator, floorDestination)
    },



    moveElevator(elevator, floorDestination) {
      const getDistanceForAnimate = () => {
        if (floorDestination === 0) return 0
        return floorDestination * 100
      }

      gsap.to(`.elevator-id-${elevator.elevatorId}`, {
        bottom: String(getDistanceForAnimate()) + 'px',
        duration: 2,
      }).then(() => {
        gsap.to(`.elevator-id-${elevator.elevatorId}`, {
          keyframes: [
            {opacity: 0},
            {opacity: 1, repeat: 2, duration: 1.5},
          ],
          ease: 'sine.out'
        }).then(() => {
          elevator.currentFloor = floorDestination

          elevator.floorDestination = null
          this.readyElevators.push(elevator)
        })
      })
    },
  },

  created() {
    for (let i = 0; i < 3; i++) {
      this.elevators.push({...elevatorExample, elevatorId: i})
      this.readyElevators.push({...elevatorExample, elevatorId: i, floorDestination: 1})
    }

    for (let i = 0; i < 7; i++) {
      this.floors.push({...floorExample, floorId: i})
    }
    this.floors.reverse()

  }

}

</script>

<style lang="scss">

* {
  margin: 0;
  box-sizing: border-box;
}

#app {
  min-height: 100vh;
  background: #524a7b;
}

.app {
  height: 100%;
}

.container {
  display: flex;
}

.elevators-container {
  min-width: 50%;
  width: fit-content;
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
}

.floors-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: stretch;
  width: 100%;

}

</style>
