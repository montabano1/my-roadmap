<!-- CloudNode.vue -->
<template>
    <div class="cloud-node" :class="nodeClass" :style="positionStyle">
      <img src="../assets/cloud.png" class="cloud-background" alt="" />
      <div class="cloud-content">
        <template v-if="status === 'done'">
          <img src="../assets/completedStar.png" class="completed-star" alt="Completed" />
        </template>
        <template v-else>
          <div class="duration">{{ duration }}</div>
          <div class="title">{{ title }}</div>
          <button 
            class="start-button" 
            @click="$emit('start')"
            :disabled="status === 'pending'"
          >
            {{ buttonText }}
          </button>
        </template>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'CloudNode',
    props: {
      title: {
        type: String,
        required: true
      },
      duration: {
        type: String,
        required: true
      },
      position: {
        type: Object,
        required: true,
        validator: prop => 'top' in prop && 'left' in prop
      },
      status: {
        type: String,
        default: 'pending',
        validator: value => ['done', 'current', 'pending'].includes(value)
      }
    },
    computed: {
      positionStyle() {
        return {
          top: this.position.top,
          left: this.position.left
        }
      },
      nodeClass() {
        return {
          'cloud-node--done': this.status === 'done',
          'cloud-node--current': this.status === 'current',
          'cloud-node--pending': this.status === 'pending'
        }
      },
      buttonText() {
        return this.status === 'current' ? 'Start' : 'Locked'
      }
    }
  }
  </script>
  
  <style scoped>
  .cloud-node {
    position: absolute;
    width: 280px;
    height: 195px;
    z-index: 2;
    transform: translate(-50%, -50%); /* Center the node on its position */
    position: absolute;
  }

  .cloud-background {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
  }

  .cloud-node--done .cloud-background {
    filter: brightness(1) saturate(1.1) hue-rotate(85deg);
  }

  .cloud-node--current .cloud-background {
    filter: none;
  }

  .cloud-node--pending .cloud-background {
    filter: brightness(1);
  }

  .cloud-node--done .cloud-content {
    color: #1b5e20;
  }

  .cloud-node--current .cloud-content {
    color: #2c3e50;
  }

  .cloud-node--pending .cloud-content {
    color: #666;
  }

  .cloud-content {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    text-align: center;
    width: 70%;
  }

  .completed-star {
    width: 40px;
    height: 40px;
    object-fit: contain;
  }

  .done-icon {
    display: flex;
    align-items: center;
    gap: 5px;
    color: #4CAF50;
    font-weight: 600;
  }

  .material-icons {
    font-size: 24px;
    color: #FFD700;
  }

  .start-button {
    margin-top: 8px;
    padding: 6px 16px;
    border: none;
    border-radius: 16px;
    color: white;
    cursor: pointer;
    font-weight: 600;
    font-size: 12px;
    transition: all 0.2s;
  }

  .start-button:disabled {
    background-color: #ccc;
    cursor: not-allowed;
  }

  .start-button:not(:disabled):hover {
    background-color: #45a049;
    transform: translateY(-1px);
  }

  .duration {
    font-size: 12px;
    color: #666;
    margin-bottom: 4px;
  }

  .title {
    font-size: 14px;
    font-weight: 600;
    margin-bottom: 8px;
  }
  
  .cloud-content {
    width: 100%;
    height: 100%;
    border-radius: 30px;
    padding: 15px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }
  
  .duration {
    font-size: 14px;
    color: #666;
    margin-bottom: 4px;
  }
  
  .title {
    font-size: 18px;
    font-weight: 600;
    margin-bottom: 12px;
    color: #333;
  }
  
  .start-button {
    background: #e0e0e0;
    border: none;
    padding: 8px 24px;
    border-radius: 15px;
    cursor: pointer;
    font-size: 14px;
    transition: background-color 0.2s;
  }
  
  .start-button:hover {
    background: #d0d0d0;
  }
  </style>