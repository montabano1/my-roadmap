<!-- RoadmapContainer.vue -->
<template>
  <div class="roadmap-container">
    <div class="road-content">
      <!-- Start Circle -->
      <div class="start-circle" :style="getStartCircleStyle()"></div>
      <!-- User Avatar -->
      <div class="user-avatar" :style="getUserAvatarStyle()">
        <div class="avatar-pulse"></div>
        <img src="../assets/userProfile.png" alt="User Avatar" />
      </div>
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
    getStartCircleStyle() {
      // Position the circle at the start of the path
      const x = 150 // Match the startX from generatePathPoints
      const y = 250 // Match the startY
      return {
        left: `${(x / this.viewBoxWidth) * 100}%`,
        top: `${(y / this.viewBoxHeight) * 100}%`,
        transform: 'translate(-50%, -50%)'
      }
    },
    getUserAvatarStyle() {
      const points = this.pathPoints
      const currentIndex = Math.floor(this.currentUnit) - 1
      
      if (currentIndex < 0 || currentIndex >= points.length - 1) return { display: 'none' }
      
      const currentPoint = points[currentIndex]
      const nextPoint = points[currentIndex + 1]
      
      const progress = 0.75
      
      const [x1, y1] = currentPoint
      const [x2, y2, type, cx1, cy1, cx2, cy2] = nextPoint
      
      let x, y
      
      if (type === 'line') {
        // Linear interpolation for straight segments
        x = x1 + (x2 - x1) * progress
        y = y1 + (y2 - y1) * progress
      } else {
        // Cubic Bezier interpolation for curves
        const t = progress
        const mt = 1 - t
        
        // Position
        x = Math.pow(mt, 3) * x1 +
            3 * Math.pow(mt, 2) * t * cx1 +
            3 * mt * Math.pow(t, 2) * cx2 +
            Math.pow(t, 3) * x2
        
        y = Math.pow(mt, 3) * y1 +
            3 * Math.pow(mt, 2) * t * cy1 +
            3 * mt * Math.pow(t, 2) * cy2 +
            Math.pow(t, 3) * y2
      }
      
      return {
        left: `${(x / this.viewBoxWidth) * 100}%`,
        top: `${(y / this.viewBoxHeight) * 100}%`,
        transform: 'translate(-50%, -50%)'
      }
    },
    generatePathPoints() {
      const startY = 250  // Starting Y position
      const points = []
      const availableWidth = this.viewBoxWidth - (2 * this.marginX)
      const startX = 150  // Starting point further left
      
      // Starting point with circle
      points.push([startX, startY, 'move'])
      
      // Extend to first node
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
.start-circle {
  position: absolute;
  width: 20px;
  height: 20px;
  background: #2c3e50;
  border-radius: 50%;
  transform: translate(-50%, -50%);
  z-index: 20;
}
.user-avatar {
  position: absolute;
  width: 80px;
  height: 80px;
  border-radius: 50%;
  background: transparent;
  box-shadow: none;
  z-index: 10;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.user-avatar img {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  object-fit: cover;
  position: relative;
  z-index: 2;
}

.avatar-pulse {
  position: absolute;
  width: 100%;
  height: 100%;
  border-radius: 50%;
  border: 2px solid rgba(114, 133, 196, 0.3);
  background: transparent;
  z-index: 1;
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0% {
    transform: scale(1);
    opacity: 0.6;
  }
  70% {
    transform: scale(1.5);
    opacity: 0.6;
  }
  100% {
    transform: scale(1.5);
    opacity: 0.6;
  }
}

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