<!-- RoadPath.vue -->
<template>
    <svg class="road-svg" :viewBox="`0 0 ${viewBoxWidth} ${viewBoxHeight}`" preserveAspectRatio="none">
      <!-- Main road -->
      <path
        :d="pathD"
        class="road-line"
        stroke="#2c3e50"
        stroke-width="60"  
        fill="none"
      />
      <!-- Dashed center line for non-completed portion -->
      <path
        :d="pathD"
        class="road-dash"
        stroke="white"
        stroke-width="4"
        stroke-dasharray="15,15"
        fill="none"
      />
      <!-- Completed portion with new style -->
      <path
        v-if="currentUnit > 1"
        :d="completedPathD"
        class="road-line completed"
        stroke="#7285c4"
        stroke-width="60"
        fill="none"
      />
      <!-- Black line for completed portion -->
      <path
        v-if="currentUnit > 1"
        :d="completedPathD"
        stroke="#2c3e50"
        stroke-width="4"
        fill="none"
      />
    </svg>
  </template>
  
  <script>
  export default {
    name: 'RoadPath',
    props: {
      viewBoxWidth: {
        type: Number,
        default: 800
      },
      viewBoxHeight: {
        type: Number,
        default: 1200
      },
      pathPoints: {
        type: Array,
        required: true
      },
      currentUnit: {
        type: Number,
        default: 1
      }
    },
    computed: {
      pathD() {
        return this.generatePath(this.pathPoints)
      },
      completedPathD() {
        // Only generate path up to current unit
        const completedPoints = this.pathPoints.slice(0, this.currentUnit + 1)
        return this.generatePath(completedPoints)
      }
    },
    methods: {
      generatePath(points) {
        return points.map((point) => {
          const [x, y, type, ...controlPoints] = point
          switch(type) {
            case 'move':
              return `M ${x} ${y}`
            case 'line':
              return `L ${x} ${y}`
            case 'curve':
              const [cx1, cy1, cx2, cy2] = controlPoints
              return `C ${cx1} ${cy1}, ${cx2} ${cy2}, ${x} ${y}`
            default:
              return ''
          }
        }).join(' ')
      }
    }
  }
  </script>
  
  <style scoped>
  .road-svg {
    width: 100%;
    height: 100%;
    position: absolute;
    left: 0;
    top: 0;
    transform-origin: left top;
    overflow: visible;
  }
  </style>