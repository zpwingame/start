<template>
  <div class="starry-background">
    <div 
      v-for="star in stars" 
      :key="star.id"
      class="star"
      :style="{
        left: star.x + 'px',
        top: star.y + 'px',
        animationDelay: star.delay + 's',
        animationDuration: star.duration + 's'
      }"
    ></div>
  </div>
</template>

<script>
export default {
  name: 'StarryBackground',
  data() {
    return {
      stars: []
    }
  },
  mounted() {
    this.generateStars()
  },
  methods: {
    generateStars() {
      const numStars = 50
      for (let i = 0; i < numStars; i++) {
        this.stars.push({
          id: i,
          x: Math.random() * window.innerWidth,
          y: Math.random() * window.innerHeight,
          delay: Math.random() * 3,
          duration: 2 + Math.random() * 3
        })
      }
    }
  }
}
</script>

<style scoped>
.starry-background {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
}

.star {
  position: absolute;
  width: 2px;
  height: 2px;
  background: rgb(159, 157, 157);
  border-radius: 50%;
  animation: twinkle infinite ease-in-out;
  box-shadow: 0 0 6px rgba(255, 255, 255, 0.8);
}

@keyframes twinkle {
  0% {
    opacity: 0.3;
    transform: scale(1);
  }
  25% {
    opacity: 0.8;
    transform: scale(1.8);
  }
  50% {
    opacity: 1;
    transform: scale(2.2);
  }
  75% {
    opacity: 0.6;
    transform: scale(1.5);
  }
  100% {
    opacity: 0;
    transform: scale(0.8);
  }
}

@keyframes float {
  0%, 100% {
    transform: translateY(0) rotate(0deg);
  }
  50% {
    transform: translateY(-20px) rotate(180deg);
  }
}
</style>