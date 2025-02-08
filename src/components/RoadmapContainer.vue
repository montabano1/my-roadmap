<!-- RoadmapContainer.vue -->
<template>
  <div class="roadmap-container">
    <div class="road-content">
      <!-- Trees -->
      <div v-for="(tree, index) in trees" :key="index" 
           class="tree" :style="getTreeStyle(tree)">
        <img :src="getTreeImage(tree.type)" :alt="`Tree ${tree.type}`" />
      </div>

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
      default: 3
    }
  },
  data() {
    return {
      trees: [],
      // Predefined tree layout pattern
      treeLayout: {
        0: ['left', 1, 0.5, 1.4],    // [side, treeType, xOffset, scale]
        1: ['right', 3, 0.8, 1.2],
        2: ['left', 2, 0.3, 1.6],
        3: ['right', 4, 0.6, 1.3],
        4: ['left', 5, 0.7, 1.5],
        5: ['right', 1, 0.4, 1.4]
      },
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
  created() {
    this.generateTrees()
  },
  methods: {
    generateTrees() {
      const trees = []
      const verticalSpacing = 300 // One tree every 300px vertically
      
      // Calculate total height needed based on last node position
      const lastNode = this.pathPoints[this.pathPoints.length - 1]
      const totalHeight = lastNode ? lastNode[1] + 300 : this.viewBoxHeight
      const numVerticalSections = Math.floor(totalHeight / verticalSpacing)
      
      // Generate trees based on predefined layout
      for (let section = 0; section < numVerticalSections; section++) {
        const layout = this.treeLayout[section % Object.keys(this.treeLayout).length]
        if (!layout) continue
        
        const [side, treeType, xOffset, scale] = layout
        const sectionY = section * verticalSpacing
        
        // Calculate x position based on side and offset
        let x
        if (side === 'left') {
          x = (this.viewBoxWidth / 3) * xOffset
        } else {
          x = this.viewBoxWidth - ((this.viewBoxWidth / 3) * xOffset)
        }
        
        // Add some slight vertical variation
        const y = sectionY + (verticalSpacing * 0.2)
        
        trees.push({
          x,
          y,
          type: treeType,
          scale
        })
      }
      
      this.trees = trees
    },

    isNearOtherTrees(x, y, otherTrees, buffer) {
      return otherTrees.some(tree => {
        const dx = x - tree.x
        const dy = y - tree.y
        const distance = Math.sqrt(dx * dx + dy * dy)
        return distance < buffer
      })
    },

    isNearPath(x, y, buffer) {
      // Simple check if point is near any path segment
      for (let i = 0; i < this.pathPoints.length - 1; i++) {
        const [x1, y1] = this.pathPoints[i]
        const [x2, y2] = this.pathPoints[i + 1]
        
        // Calculate distance from point to line segment
        const distance = this.pointToLineDistance(x, y, x1, y1, x2, y2)
        if (distance < buffer) return true
      }
      return false
    },

    pointToLineDistance(x, y, x1, y1, x2, y2) {
      const A = x - x1
      const B = y - y1
      const C = x2 - x1
      const D = y2 - y1
      
      const dot = A * C + B * D
      const len_sq = C * C + D * D
      
      let param = -1
      if (len_sq !== 0) param = dot / len_sq
      
      let xx, yy
      
      if (param < 0) {
        xx = x1
        yy = y1
      } else if (param > 1) {
        xx = x2
        yy = y2
      } else {
        xx = x1 + param * C
        yy = y1 + param * D
      }
      
      const dx = x - xx
      const dy = y - yy
      return Math.sqrt(dx * dx + dy * dy)
    },

    getTreeStyle(tree) {
      return {
        left: `${(tree.x / this.viewBoxWidth) * 100}%`,
        top: `${(tree.y / this.viewBoxHeight) * 100}%`,
        transform: `translate(-50%, -50%) scale(${tree.scale})`
      }
    },

    getTreeImage(type) {
      // Using static imports for tree images
      const treeImages = {
        1: new URL('../assets/tree1.png', import.meta.url).href,
        2: new URL('../assets/tree2.png', import.meta.url).href,
        3: new URL('../assets/tree3.png', import.meta.url).href,
        4: new URL('../assets/tree4.png', import.meta.url).href,
        5: new URL('../assets/tree5.png', import.meta.url).href
      }
      return treeImages[type]
    },
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
.tree {
  position: absolute;
  width: 160px;
  height: 160px;
  z-index: 5;
  transition: transform 0.3s ease;
}

.tree img {
  width: 100%;
  height: 100%;
  object-fit: contain;
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
  background: whitesmoke;
  z-index: 1;
  animation: pulse 1.5s infinite;
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