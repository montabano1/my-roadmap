<!-- RoadmapContainer.vue -->
<template>
  <div class="roadmap-container">
    <div class="road-content">
      <!-- Path Component -->
      <RoadPath
        :path-points="pathPoints"
        :view-box-width="viewBoxWidth"
        :view-box-height="viewBoxHeight"
        :current-unit="currentUnit"
      />
      
      <!-- Cloud Nodes -->
      <CloudNode
        v-for="(unit, index) in learningUnits"
        :key="index"
        :title="unit.title"
        :duration="unit.duration"
        :position="getNodePosition(index)"
        :status="getNodeStatus(index)"
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
  props: {
    currentUnit: {
      type: Number,
      default: 2
    }
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
        [700, 300, 'curve', 500, 150, 700, 250],    // Node 2: Day 2 R&W (right-down)
        [100, 500, 'curve', 600, 400, 200, 450],    // Node 3: Day 3 Math (left-down)
        [700, 700, 'curve', 200, 600, 600, 650],    // Node 4: Day 4 R&W (right-down)
        [100, 900, 'curve', 600, 800, 200, 850]     // Node 5: Day 5 Math (left-down)
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
    },
    getNodeStatus(index) {
      if (index < this.currentUnit - 1) return 'done'
      if (index === this.currentUnit - 1) return 'current'
      return 'pending'
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