<script>
import InputComponent from './components/InputComponent.vue'
import ButtonComponent from './components/ButtonComponent.vue'
import { ref } from 'vue'

export default {
  components: {
    InputComponent,
    ButtonComponent
  },
  setup() {
    const canvasRef = ref(null)
    return { canvasRef }
  },
  data() {
    return {
      aircrafts: [
        {
          id: 1,
          number: 'RA-12345',
          flights: [
            {
              id: 2,
              departure: '2024-02-11 00:00',
              arrival: '2024-02-11 02:00'
            },
            {
              id: 3,
              departure: '2024-02-11 03:00',
              arrival: '2024-02-11 05:00'
            }
          ]
        },
        {
          id: 1,
          number: 'RA-12346',
          flights: [
            {
              id: 2,
              departure: '2024-02-11 00:00',
              arrival: '2024-02-11 03:00'
            },
            {
              id: 3,
              departure: '2024-02-11 04:00',
              arrival: '2024-02-11 06:00'
            }
          ]
        }
      ]
    }
  },
  methods: {
    addAircraft() {
      this.aircrafts.push({
        id: Date.now(),
        number: '',
        flights: []
      })
      this.renderSheduleCanvas()
    },
    addFlight(aircraftId) {
      const properAircraft = this.aircrafts.find((item) => item.id === aircraftId)
      if (properAircraft) {
        properAircraft.flights.push({
          id: Date.now(),
          departure: '2024-02-11 00:00',
          arrival: '2024-02-11 02:00'
        })
      }
      this.renderSheduleCanvas()
    },
    getDateMinMax() {
      const allFlightsDates = this.aircrafts
        .map((aircraft) => {
          return aircraft.flights.map((flight) => [flight.departure, flight.arrival]).flat()
        })
        .flat()

      const uniqueDates = Array.from(new Set(allFlightsDates))
      const dates = uniqueDates.map((date) => new Date(date))
      const min = Math.min.apply(null, dates)
      const max = Math.max.apply(null, dates)

      return { min, max }
    },

    renderSheduleCanvas() {
      this.canvasRef.height = this.aircrafts.length * 100 + (this.aircrafts.length - 1) * 8
      const blockHeight = 100
      const canvasWidth = this.canvasRef.width
      const datesMinMax = this.getDateMinMax()
      const dateRangeMilliseconds = datesMinMax.max - datesMinMax.min
      const dateRangeMinutes = dateRangeMilliseconds / 1000 / 60
      const unit = canvasWidth / dateRangeMinutes

      const ctx = this.canvasRef.getContext('2d')
      ctx.reset()
      ctx.fillStyle = '#03dac6'

      this.aircrafts.forEach((aircraft, index) => {
        aircraft.flights.forEach((flight) => {
          const departureDate = new Date(flight.departure).getTime() / 1000 / 60
          const arrivalDate = new Date(flight.arrival).getTime() / 1000 / 60
          const x = (departureDate - datesMinMax.min / 1000 / 60) * unit
          const y = index * 108
          const width = (arrivalDate - departureDate) * unit

          ctx.fillRect(x, y, width, blockHeight)
        })
      })
    }
  },
  mounted() {
    this.renderSheduleCanvas()
  }
}
</script>

<template>
  <header>
    <h1 class="h1">Перелеты воздушных судов</h1>
  </header>

  <main>
    <div class="elevation" v-for="aircraft in aircrafts" v-bind:key="aircraft.id">
      <InputComponent label="Бортовой номер" class="aircraft" v-model="aircraft.number" />

      <h4 class="h4">Расписание</h4>
      <div class="flight-container" v-for="flight in aircraft.flights" v-bind:key="flight.id">
        <InputComponent
          label="Вылет"
          class="flight-input"
          v-model="flight.departure"
          @render-shedule-canvas="renderSheduleCanvas"
        />
        <InputComponent
          label="Посадка"
          class="flight-input"
          v-model="flight.arrival"
          @render-shedule-canvas="renderSheduleCanvas"
        />
      </div>
      <ButtonComponent label="Добавить перелет" @click="addFlight(aircraft.id)" />
    </div>
    <ButtonComponent class="addAircraftButton" label="+" @click="addAircraft" />
    <div class="schedule">
      <div class="aircrafts-list">
        <div class="aircraft-item" v-for="aircraft in aircrafts" v-bind:key="aircraft.id">
          {{ aircraft.number }}
        </div>
      </div>
      <canvas ref="canvasRef" width="600"></canvas>
    </div>
  </main>
</template>

<style scoped>
.elevation {
  border: 2px solid var(--elevation-color);
  border-radius: 4px;
  padding: 16px;
  margin-bottom: 16px;
}

.aircraft {
  margin-bottom: 24px;
}

.h1 {
  font-size: 48px;
  margin-bottom: 24px;
  font-weight: 700;
}
.h4 {
  margin-bottom: 16px;
  font-size: 34px;
}

.flight-container {
  display: flex;
  justify-content: space-between;
  margin-bottom: 16px;
  gap: 24px;
}

.flight-input {
  width: 100%;
}

.addAircraftButton {
  position: fixed;
  bottom: 16px;
  right: 16px;
  border-radius: 50%;
  width: 56px;
  height: 56px;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 20px;
  font-weight: 700;
  padding: 0;
}

.schedule {
  display: flex;
  gap: 8px;
}

.aircrafts-list {
  width: 100px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}
.aircraft-item {
  height: 100px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid var(--elevation-color);
}
</style>
