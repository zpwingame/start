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
    <div class="floating-planets">
      <div class="background-planet planet-1"></div>
      <div class="background-planet planet-2"></div>
    </div>
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
      const numStars = 150
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
  background: white;
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

.floating-planets {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 2;
}

.background-planet {
  position: absolute;
  border-radius: 50%;
  background: radial-gradient(circle at 30% 30%, rgba(100, 200, 255, 0.3), rgba(50, 100, 200, 0.1));
  animation: float infinite ease-in-out;
}

.planet-1 {
  width: 120px;
  height: 120px;
  top: 10%;
  right: 15%;
  animation-duration: 8s;
}

.planet-2 {
  width: 80px;
  height: 80px;
  top: 70%;
  left: 10%;
  animation-duration: 6s;
  animation-delay: -2s;
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