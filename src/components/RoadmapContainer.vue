<!-- RoadmapContainer.vue -->
<template>
  <div class="roadmap-container">
    <div class="road-content">
      <!-- Path Component -->
      <RoadPath
        :path-points="pathPoints"
        :view-box-width="viewBoxWidth"
        :view-box-height="viewBoxHeight"
      />
      
      <!-- Cloud Nodes -->
      <CloudNode
        v-for="(unit, index) in learningUnits"
        :key="index"
        :title="unit.title"
        :duration="unit.duration"
        :position="getNodePosition(index)"
        @start="handleStart(index)"
      />
    </div>
  </div>
</template>

<script>
import RoadPath from './RoadPath.vue'
import CloudNode from './CloudNode.vue'

export default {
  name: 'RoadmapContainer',
  components: {
    RoadPath,
    CloudNode
  },
  data() {
    return {
      viewBoxWidth: 800,
      viewBoxHeight: 1200,
      learningUnits: [
        { title: 'Day 1 Math', duration: '20min' },
        { title: 'Day 2 R&W', duration: '20min' },
        { title: 'Day 3 Math', duration: '20min' },
        { title: 'Day 4 R&W', duration: '20min' },
        { title: 'Day 5 Math', duration: '20min' }
      ],
      pathPoints: [
        [50, 100, 'move'],                           // Starting point
        [300, 100, 'line'],                          // Node 1: Day 1 Math
        [600, 300, 'curve', 450, 100, 600, 200],    // Node 2: Day 2 R&W
        [300, 500, 'curve', 450, 400, 300, 400],    // Node 3: Day 3 Math
        [600, 700, 'curve', 300, 600, 600, 600],    // Node 4: Day 4 R&W
        [300, 900, 'curve', 600, 800, 300, 800]     // Node 5: Day 5 Math
      ]
    }
  },
  methods: {
    getNodePosition(index) {
      // Get the corresponding path point for this node
      const point = this.pathPoints[index + 1] // +1 because first point is "move"
      if (!point) return { top: '0px', left: '0px' }

      // Convert SVG viewBox coordinates to percentage
      const x = (point[0] / this.viewBoxWidth) * 100
      const y = (point[1] / this.viewBoxHeight) * 100

      return {
        left: `${x}%`,
        top: `${y}%`
      }
    },
    handleStart(index) {
      console.log(`Starting unit ${index + 1}`)
      // Handle start logic here
    }
  }
}
</script>

<style scoped>
.roadmap-container {
  width: 100%;
  height: 100vh;
  background-color: #8b9ddb;
  overflow: hidden;
  position: relative;
}

.road-content {
  width: 100%;
  height: 100%;
  position: relative;
  overflow: auto;
}
</style>