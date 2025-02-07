<!-- CloudNode.vue -->
<template>
    <div class="cloud-node" :style="positionStyle">
      <div class="cloud-content">
        <div class="duration">{{ duration }}</div>
        <div class="title">{{ title }}</div>
        <button class="start-button" @click="$emit('start')">
          Start
        </button>
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
      }
    },
    computed: {
      positionStyle() {
        return {
          top: this.position.top,
          left: this.position.left
        }
      }
    }
  }
  </script>
  
  <style scoped>
  .cloud-node {
    position: absolute;
    width: 200px;
    height: 120px;
    z-index: 2;
    transform: translate(-50%, -50%); /* Center the node on its position */
  }
  
  .cloud-content {
    background: white;
    width: 100%;
    height: 100%;
    border-radius: 30px;
    padding: 15px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
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