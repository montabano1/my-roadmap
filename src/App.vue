<!-- App.vue -->
<template>
  <div class="app">
    <RoadmapContainer
      v-model:current-unit="currentUnit"
      v-model:just-completed="justCompleted"
    />
  </div>
</template>

<script>
import RoadmapContainer from './components/RoadmapContainer.vue'

export default {
  name: 'App',
  components: {
    RoadmapContainer
  },
  data() {
    return {
      currentUnit: 2,
      justCompleted: true
    }
  },
  mounted() {
    // Simulate returning from a completed unit
    this.justCompleted = true;
    
    // After a brief delay, trigger the animation to 75%
    setTimeout(() => {
      this.justCompleted = false;
    }, 1500);
  }
}
</script>

<style>
/* Reset ALL default spacing */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* Reset specific elements that might add space */
html, 
body {
  margin: 0;
  padding: 0;
  overflow: hidden;
  width: 100%;
  height: 100%;
}

body {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* App container */
.app {
  width: 100vw;
  height: 100vh;
  display: block;
  position: relative;
  overflow: hidden;
  background-color: #8b9ddb;
}

#app {
  margin: 0;
  padding: 0;
}
</style>