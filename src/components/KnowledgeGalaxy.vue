<template>
  <div class="knowledge-galaxy" @wheel="handleScroll">
    <svg class="curve-path" :viewBox="`0 0 ${svgWidth} ${svgHeight}`">
      <!-- S曲线路径 -->
      <path
        :d="curvePath"
        stroke="url(#curveGradient)"
        stroke-width="4"
        fill="none"
        class="main-curve"
      />
      
      <!-- 渐变定义 -->
      <defs>
        <linearGradient id="curveGradient" x1="0%" y1="0%" x2="0%" y2="100%">
          <!-- 已完成部分 - 淡蓝色发光 -->
          <stop offset="0%" style="stop-color:#87ceeb;stop-opacity:1" />
          <!-- 学习中/待学习部分 - 蓝绿色 -->
          <stop offset="60%" style="stop-color:#20b2aa;stop-opacity:1" />
          <!-- 待解锁部分 - 浅蓝色 -->
          <stop offset="100%" style="stop-color:#add8e6;stop-opacity:0.6" />
        </linearGradient>
        
        <filter id="glow" x="-50%" y="-50%" width="200%" height="200%">
          <feGaussianBlur stdDeviation="3" result="coloredBlur"/>
          <feMerge> 
            <feMergeNode in="coloredBlur"/>
            <feMergeNode in="SourceGraphic"/>
          </feMerge>
        </filter>
      </defs>
    </svg>

    <div class="planets-container" :style="{ transform: `translateY(${scrollOffset}px)` }">
      <PlanetComponent
        v-for="(planet, index) in planets"
        :key="planet.id"
        :planet="planet"
        :position="getScrollAdjustedPosition(planet.position, index)"
        @planet-click="handlePlanetClick"
      />
    </div>
  </div>
</template>

<script>
import PlanetComponent from './PlanetComponent.vue'

export default {
  name: 'KnowledgeGalaxy',
  components: {
    PlanetComponent
  },
  data() {
    return {
      scrollOffset: 0,
      svgWidth: 400,
      svgHeight: 1200,
      planets: [
        { id: 1, type: 'locked', title: '未解锁课程', position: { x: 200, y: 100 } },
        { id: 2, type: 'locked', title: '未解锁课程', position: { x: 150, y: 200 } },
        { id: 3, type: 'learning', title: '学习中', position: { x: 250, y: 350 } },
        { id: 4, type: 'available', title: '待学习', position: { x: 180, y: 450 } },
        { id: 5, type: 'completed', title: '已完成', position: { x: 220, y: 600 } },
        { id: 6, type: 'completed', title: '已完成', position: { x: 160, y: 750 } },
        { id: 7, type: 'completed', title: '已完成', position: { x: 240, y: 900 } }
      ]
    }
  },
  computed: {
    curvePath() {
      const width = this.svgWidth
      const height = this.svgHeight
      const centerX = width / 2
      
      return `M ${centerX} 50
              Q ${centerX - 80} 200, ${centerX} 300
              T ${centerX} 500
              Q ${centerX + 80} 700, ${centerX} 800
              T ${centerX} ${height - 50}`
    }
  },
  mounted() {
    this.updatePlanetPositions()
    window.addEventListener('resize', this.updatePlanetPositions)
  },
  beforeUnmount() {
    window.removeEventListener('resize', this.updatePlanetPositions)
  },
  methods: {
    handleScroll(event) {
      event.preventDefault()
      const delta = event.deltaY
      const maxScroll = 600
      const minScroll = -300
      
      this.scrollOffset -= delta * 0.5
      this.scrollOffset = Math.max(minScroll, Math.min(maxScroll, this.scrollOffset))
    },
    
    updatePlanetPositions() {
      const centerX = window.innerWidth / 2
      this.svgWidth = window.innerWidth
      
      this.planets.forEach((planet, index) => {
        const progress = index / (this.planets.length - 1)
        const position = this.getPointOnCurve(progress)
        
        planet.position = {
          x: centerX + position.x,
          y: position.y
        }
      })
    },

    getPointOnCurve(t) {
      const width = this.svgWidth
      const height = this.svgHeight
      const centerX = width / 2
      
      // S曲线参数化路径计算
      const y = 50 + t * (height - 100)
      let x = 0
      
      // 分段计算S曲线的x偏移
      if (t < 0.25) {
        // 第一段曲线
        const segmentT = t / 0.25
        x = -80 * Math.sin(segmentT * Math.PI * 0.5)
      } else if (t < 0.5) {
        // 第二段曲线  
        const segmentT = (t - 0.25) / 0.25
        x = -80 * Math.cos(segmentT * Math.PI * 0.5)
      } else if (t < 0.75) {
        // 第三段曲线
        const segmentT = (t - 0.5) / 0.25
        x = 80 * Math.sin(segmentT * Math.PI * 0.5)
      } else {
        // 第四段曲线
        const segmentT = (t - 0.75) / 0.25
        x = 80 * Math.cos(segmentT * Math.PI * 0.5)
      }
      
      return { x, y }
    },

    getScrollAdjustedPosition(originalPosition, index) {
      const progress = (index / (this.planets.length - 1)) + (this.scrollOffset / 1000)
      const clampedProgress = Math.max(0, Math.min(1, progress))
      const curvePosition = this.getPointOnCurve(clampedProgress)
      const centerX = window.innerWidth / 2
      
      return {
        x: centerX + curvePosition.x,
        y: curvePosition.y - this.scrollOffset
      }
    },
    
    handlePlanetClick(planet) {
      if (planet.type === 'locked') {
        this.$root.showModal('请先完成全部必修课程，才能解锁考试哦')
      } else {
        console.log(`进入${planet.title}详情页面`)
      }
    }
  }
}
</script>

<style scoped>
.knowledge-galaxy {
  position: relative;
  width: 100%;
  height: 100vh;
  z-index: 10;
}

.curve-path {
  position: fixed;
  top: 0;
  left: 50%;
  transform: translateX(-50%);
  z-index: 5;
  filter: url(#glow);
}

.planets-container {
  position: relative;
  width: 100%;
  height: 100%;
  transition: transform 0.3s ease-out;
  z-index: 15;
}

.main-curve {
  filter: drop-shadow(0 0 8px rgba(135, 206, 235, 0.6));
}
</style>