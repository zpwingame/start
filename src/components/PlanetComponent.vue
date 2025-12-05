<template>
  <div 
    class="planet-wrapper"
    :style="{ left: position.x + 'px', top: position.y + 'px' }"
  >
    <div 
      class="planet"
      :class="[`planet-${planet.type}`, { 'planet-shake': isShaking }]"
      @click="handleClick"
      @animationend="isShaking = false"
    >
      <div class="planet-surface">
        <div class="planet-glow" v-if="planet.type === 'completed'"></div>
        <div class="planet-rings" v-if="planet.type === 'learning'"></div>
        <div class="lock-icon" v-if="planet.type === 'locked'">🔒</div>
      </div>
      <div class="planet-label" v-if="planet.type === 'learning'">
        {{ planet.title }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'PlanetComponent',
  props: {
    planet: {
      type: Object,
      required: true
    },
    position: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      isShaking: false,
      isHighlighted: false
    }
  },
  methods: {
    handleClick() {
      if (this.planet.type === 'locked') {
        this.isShaking = true
      } else if (this.planet.type === 'learning' || this.planet.type === 'available') {
        this.isHighlighted = true
        setTimeout(() => {
          this.isHighlighted = false
        }, 600)
      }
      
      this.$emit('planet-click', this.planet)
    }
  }
}
</script>

<style scoped>
.planet-wrapper {
  position: absolute;
  transform: translate(-50%, -50%);
  z-index: 20;
}

.planet {
  width: 60px;
  height: 60px;
  position: relative;
  cursor: pointer;
  transition: all 0.3s ease;
}

.planet-surface {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  position: relative;
  overflow: hidden;
}

/* 已完成星球 - 淡蓝色发光 */
.planet-completed .planet-surface {
  background: radial-gradient(circle at 30% 30%, #87ceeb, #4682b4);
  box-shadow: 
    0 0 20px rgba(135, 206, 235, 0.8),
    inset -10px -10px 20px rgba(0, 0, 0, 0.3);
  animation: completedGlow 3s ease-in-out infinite;
}

.planet-completed:hover {
  transform: scale(1.1);
}

/* 学习中星球 - 蓝绿色 + 上下摆动 */
.planet-learning .planet-surface {
  background: radial-gradient(circle at 30% 30%, #20b2aa, #008b8b);
  box-shadow: 
    0 0 25px rgba(32, 178, 170, 0.9),
    inset -8px -8px 16px rgba(0, 0, 0, 0.3);
  animation: learningFloat 2s ease-in-out infinite;
}

.planet-learning:hover {
  transform: scale(1.2);
  box-shadow: 0 0 30px rgba(32, 178, 170, 1);
}

/* 待学习星球 - 蓝绿色 */
.planet-available .planet-surface {
  background: radial-gradient(circle at 30% 30%, #20b2aa, #008b8b);
  box-shadow: 
    0 0 15px rgba(32, 178, 170, 0.6),
    inset -8px -8px 16px rgba(0, 0, 0, 0.3);
}

.planet-available:hover {
  transform: scale(1.2);
  box-shadow: 0 0 25px rgba(32, 178, 170, 0.8);
}

/* 未解锁星球 - 浅蓝色 */
.planet-locked .planet-surface {
  background: radial-gradient(circle at 30% 30%, #add8e6, #87ceeb);
  box-shadow: 
    0 0 10px rgba(173, 216, 230, 0.4),
    inset -8px -8px 16px rgba(0, 0, 0, 0.2);
  opacity: 0.7;
}

/* 锁定图标 */
.lock-icon {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 20px;
  color: rgba(255, 255, 255, 0.8);
}

/* 星球光晕效果 */
.planet-glow {
  position: absolute;
  top: -10px;
  left: -10px;
  right: -10px;
  bottom: -10px;
  border-radius: 50%;
  background: radial-gradient(circle, rgba(135, 206, 235, 0.3), transparent 70%);
  animation: glowPulse 2s ease-in-out infinite;
}

/* 学习中星球的环 */
.planet-rings {
  position: absolute;
  top: -15px;
  left: -15px;
  right: -15px;
  bottom: -15px;
  border: 2px solid rgba(32, 178, 170, 0.5);
  border-radius: 50%;
  animation: ringRotate 4s linear infinite;
}

/* 星球标签 */
.planet-label {
  position: absolute;
  top: 70px;
  left: 50%;
  transform: translateX(-50%);
  background: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 4px 8px;
  border-radius: 12px;
  font-size: 12px;
  white-space: nowrap;
  border: 1px solid rgba(32, 178, 170, 0.8);
}

/* 摇摆动画 */
.planet-shake {
  animation: shake 0.5s ease-in-out;
}

/* 动画定义 */
@keyframes completedGlow {
  0%, 100% { 
    box-shadow: 
      0 0 20px rgba(135, 206, 235, 0.8),
      inset -10px -10px 20px rgba(0, 0, 0, 0.3);
  }
  50% { 
    box-shadow: 
      0 0 30px rgba(135, 206, 235, 1),
      inset -10px -10px 20px rgba(0, 0, 0, 0.3);
  }
}

@keyframes learningFloat {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-8px); }
}

@keyframes glowPulse {
  0%, 100% { opacity: 0.5; transform: scale(1); }
  50% { opacity: 0.8; transform: scale(1.1); }
}

@keyframes ringRotate {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-5px); }
  75% { transform: translateX(5px); }
}
</style>