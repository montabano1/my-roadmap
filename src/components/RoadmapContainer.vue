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
      // Road curve parameters
      curveIntensity: 150,    // How much the curves bend (was 80)
      snakeOffset: 100,       // How far nodes offset from center
      verticalGap: 500,       // Gap between nodes
      marginX: 250,           // Side margins
      learningUnits: [
        { title: 'Day 1 Math', duration: '20min' },
        { title: 'Day 2 R&W', duration: '20min' },
        { title: 'Day 3 Math', duration: '20min' },
        { title: 'Day 4 R&W', duration: '20min' },
        { title: 'Day 5 Math', duration: '20min' },
        { title: 'Day 6 R&W', duration: '20min' },
        { title: 'Day 7 Math', duration: '20min' },
        { title: 'Day 8 R&W', duration: '20min' },
        { title: 'Day 9 Math', duration: '20min' },
        { title: 'Day 10 R&W', duration: '20min' },
        { title: 'Day 11 Math', duration: '20min' },
        { title: 'Day 12 R&W', duration: '20min' },
        { title: 'Day 13 Math', duration: '20min' },
        { title: 'Day 14 R&W', duration: '20min' },
        { title: 'Day 15 Math', duration: '20min' },
        { title: 'Day 16 R&W', duration: '20min' },
        { title: 'Day 17 Math', duration: '20min' },
      ]
    }
  },
  computed: {
    pathPoints() {
      return this.generatePathPoints()
    }
  },
  methods: {
    generatePathPoints() {
      const startY = 250  // Starting Y position
      const points = []
      const availableWidth = this.viewBoxWidth - (2 * this.marginX)
      
      // Starting point with user avatar
      points.push([this.marginX, startY, 'move'])
      
      // First node extends right
      points.push([this.marginX + 200, startY, 'line'])
      
      // Generate subsequent points
      for (let i = 1; i < this.learningUnits.length; i++) {
        const cyclePosition = i % 14 // Complete cycle is 14 units
        const currentY = startY + i * this.verticalGap
        
        // Calculate which segment we're in (0-6 for right, 7-13 for left)
        const segment = Math.floor(cyclePosition / 7)
        const progressInSegment = cyclePosition % 7
        
        // Calculate base X position
        let baseX
        if (segment === 0) { // First 7 units - moving right
          baseX = this.marginX + (availableWidth * (progressInSegment / 6))
        } else { // Next 7 units - moving left
          baseX = (this.viewBoxWidth - this.marginX) - (availableWidth * (progressInSegment / 6))
        }
        
        // Add snake effect
        const isEven = i % 2 === 0
        const targetX = baseX + (isEven ? this.snakeOffset : -this.snakeOffset)
        
        // Get previous point for control points calculation
        const prevPoint = points[points.length - 1]
        const prevX = prevPoint[0]
        const prevY = prevPoint[1]
        
        // Calculate single control point for smooth curve
        const midY = (prevY + currentY) / 2
        const midX = (prevX + targetX) / 2
        const offset = isEven ? this.curveIntensity : -this.curveIntensity
        
        points.push([
          targetX,
          currentY,
          'curve',
          midX + offset,
          midY,
          midX + offset,
          midY
        ])
      }
      
      return points
    },
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
  min-width: 800px;
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