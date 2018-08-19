<template>
  <div class="py-8">
    <button>Add new Location</button>
    <!-- Cards showing points -->
    <div>

      <!-- Single card created for a point -->
      <div class="bg-white px-4 mt-4 pt-4 shadow rounded">
        <!-- Input the values for each point -->
        <form @submit="preventDefault" class="mb-4 flex justify-between">
          <div>
            <label for="" class="font-bold">Enter Name</label> <br>
            <input type="text" ref="defaultInput" v-model="point.name" placeholder="A" class="bg-grey-lightest border border-grey rounded py-1 px-4  mt-2">
          </div>
          <div class="mr-4">
            <label for="" class="font-bold">Enter Direction</label>
            <div class="flex">
              <input type="text" placeholder="0" v-model.number="point.direction.degrees" class="bg-grey-lightest border border-grey rounded py-1 px-1 mt-2 mr-1 w-10">
              <input type="text" placeholder="0" v-model.number="point.direction.minutes" class="bg-grey-lightest border border-grey rounded py-1 px-1 mt-2 mr-1 w-10">
              <input type="text" placeholder="0" v-model.number="point.direction.seconds" class="bg-grey-lightest border border-grey rounded py-1 px-1 mt-2 mr-1 w-10">
            </div>
          </div>

          <div>
            <label for="" class="font-bold">Enter Distance</label> <br>
            <input type="text" placeholder="123" v-model.number="point.distance" class="bg-grey-lightest border border-grey rounded py-1 px-4 mt-2">
          </div>
          <button @click="addPoint" class="bg-purple flex-no-shrink self-end h-10 w-10 text-white rounded-full shadow">+</button>
        </form>

        <div class="flex">
          <!-- A list of the distace and direction -->
          <div class="flex-1 py-6 pr-3 rounded">
            <div class="flex justify-between font-bold border-b pb-2 mb-2">
              <span>Direction</span>
              <span>Distance</span>
              <span>Name</span>
            </div>

            <div class="flex justify-between" v-for="(p, i) in currentStand.points" :key="i">
              <span class="mr-2">{{ p.direction.degrees }}:{{ p.direction.minutes }}:{{ p.direction.seconds }}</span>
              <span>{{ p.distance }}</span>
              <span>{{ p.name }}</span>
            </div>
          </div>
          <!-- Calculated cross multiplication stuff -->
          <div class="flex-1 py-6 pl-3">
            <div class="flex justify-between font-bold border-b pb-2 mb-2">
              <span>Y</span>
              <span>X</span>
            </div>
            
            <div class="flex justify-between" v-for="(p, i) in currentStand.points" :key="i">
              <span>{{ p.coordinates.y }}</span>
              <span>{{ p.coordinates.x }}</span>
            </div>
          </div>
        </div>

        <!-- The calculated area -->
        <div class="-mx-4 p-4 bg-blue-lightest flex justify-between items-center">
          <button @click="addStand" class="bg-purple  py-2 px-6 rounded shadow text-white" :disabled="currentStand.points.length < 3">Add this location</button>
          <div>
            <span class="font-bold">Area:</span><span> {{ area !== null ? area : 'not yet calculated' }}</span>
          </div>
        </div>
        <hr>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  mounted () {
    // [{ dis: 10, dir: 270 }, { dis: 20, dir: 0 }, { dis: 10, dir: 90 }, { dis: 20, dir: 180 }].forEach(x => {
    //   let p = {
    //     name: 'A',
    //     coordinates: {
    //       x: 0,
    //       y: 0
    //     },
    //     distance: 0,
    //     direction: {
    //       degrees: 0,
    //       minutes: 0,
    //       seconds: 0
    //     }
    //   }
    //   p.direction.degrees = x.dir
    //   p.distance = x.dis
    //   this.currentStand.points.push(p)
    // })

    // console.log('The last one, using new algorithm', this.calculateArea(this.calculateCoordinates()))
  },
  data () {
    return {
      point: {
        name: 'A',
        coordinates: {
          x: 0,
          y: 0
        },
        distance: 0,
        direction: {
          degrees: 0,
          minutes: 0,
          seconds: 0
        }
      },
      currentStand: {
        stand_number: 0,
        points: []
      },
      stands: [],
      area: null
    }
  },

  methods: {
    addPoint () {
      this.currentStand.points.push(this.point)
      this.point =  {
        name: 'A',
        coordinates: {
          x: 0,
          y: 0
        },
        distance: 0,
        direction: {
          degrees: 0,
          minutes: 0,
          seconds: 0
        }
      }
      this.$refs.defaultInput.focus()
    }, 
    addStand () {
      this.area = this.calculateArea(this.calculateCoordinates())
      // this.stands.push(this.currentStand)
    },
    preventDefault (e) {
      e.preventDefault()
    },
    toDegrees (direction) {
      let { degrees, minutes, seconds } = direction
      return (degrees + (minutes / 60) + (seconds / 3600)) * (Math.PI / 180)
    },
    calculateCoordinates () {
      let coordinates = []
      for (let i = 0; i < this.currentStand.points.length; i++) {
        const point = this.currentStand.points[i]
        let x, y

        const dx = point.distance * Math.cos(this.toDegrees(point.direction))
        const dy = point.distance * Math.sin(this.toDegrees(point.direction))
        point.coordinates.x = x = point.coordinates.x + dx
        point.coordinates.y = y = point.coordinates.y + dy
        coordinates.push({ x, y })
      }

      return coordinates
    },
    calculateArea (coordinates = []) {
      let SumOfDifferences = 0
      for (let i = 0; i < coordinates.length; i++) {
        let X1, X2, Y1, Y2

        if (i === coordinates.length - 1) {
          X1 = coordinates[i].x // Xn
          X2 = coordinates[0].x
          Y1 = coordinates[i].y
          Y2 = coordinates[0].y
        } else {
          X1 = coordinates[i].x
          X2 = coordinates[i + 1].x
          Y1 = coordinates[i].y
          Y2 = coordinates[i + 1].y

        }
      
        console.log(`${X1} * ${Y2} - ${Y1} * ${X2} = ${((X1 * Y2) - (Y1 * X2))}`)
        // using formula
        // ((	x1 * y2 −	y1 * x2 ) 	 +	 	(	x2 * y3 −	y2 * x3 ) 	.....	 	+	 	 (	xn * y1 −	yn * x1 ) ) / 2
        SumOfDifferences += ((X1 * Y2) - (Y1 * X2))
      }
      
      return Math.abs(SumOfDifferences / 4) // original implementation had SumOfDifferences/2
    }

  }
}
</script>

<style>

</style>
