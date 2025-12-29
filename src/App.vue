<template>
  <div class="galaxy-container">
    <StarryBackground />
    <KnowledgeGalaxy />
  </div>
</template>

<script>
import StarryBackground from './components/StarryBackground.vue'
import KnowledgeGalaxy from './components/KnowledgeGalaxy.vue'

export default {
  name: 'App',
  components: {
    StarryBackground,
    KnowledgeGalaxy,
  },
  data() {
    return {

    }
  },
  methods: {

  },
  mounted() {

  }
}
</script>

<style>
.galaxy-container {
  width: 100vw;
  height: 100vh;
  position: relative;
  background-image: url(./assets/study-bg.png);
  background-size: cover;
}
</style>