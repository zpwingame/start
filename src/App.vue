<template>
  <div class="galaxy-container">
    <StarryBackground />
    <KnowledgeGalaxy />
    <PlanetModal 
      v-if="showModal"
      :message="modalMessage"
      @close="closeModal"
    />
  </div>
</template>

<script>
import StarryBackground from './components/StarryBackground.vue'
import KnowledgeGalaxy from './components/KnowledgeGalaxy.vue'
import PlanetModal from './components/PlanetModal.vue'

export default {
  name: 'App',
  components: {
    StarryBackground,
    KnowledgeGalaxy,
    PlanetModal
  },
  data() {
    return {
      showModal: false,
      modalMessage: ''
    }
  },
  methods: {
    closeModal() {
      this.showModal = false
    }
  },
  mounted() {
    this.$root.showModal = (message) => {
      this.modalMessage = message
      this.showModal = true
    }
  }
}
</script>

<style>
.galaxy-container {
  width: 100vw;
  height: 100vh;
  position: relative;
  overflow: hidden;
  background: radial-gradient(ellipse at center, #1e3a5f 0%, #0a0e1a 100%);
}
</style>