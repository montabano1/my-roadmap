<!-- RoadPath.vue -->
<template>
    <svg class="road-svg" :viewBox="`0 0 ${viewBoxWidth} ${viewBoxHeight}`" preserveAspectRatio="none">
      <!-- Main road -->
      <path
        :d="pathD"
        class="road-line"
        stroke="#2c3e50"
        stroke-width="30"  
        fill="none"
      />
      <!-- Dashed center line -->
      <path
        :d="pathD"
        class="road-dash"
        stroke="white"
        stroke-width="2"
        stroke-dasharray="10,10"
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
      }
    },
    computed: {
      pathD() {
        return this.generatePath(this.pathPoints)
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