<template>
  <div class="knowledge-galaxy" @wheel="handleScroll">
    <svg class="curve-path" :viewBox="`0 0 ${svgWidth} ${svgHeight}`">
      <!-- S曲线路径 -->
      <path
        :d="curvePath"
        stroke="url(#curveGradient)"
        stroke-width="12"
        fill="none"
        class="main-curve"
      />
      
      <!-- 渐变定义 -->
      <defs>
        <linearGradient id="curveGradient" x1="0%" y1="0%" x2="0%" y2="100%">
          <stop offset="0%" style="stop-color:rgba(83, 152, 255, 0.2);stop-opacity:1" />
          <stop offset="100%" style="stop-color:rgba(83, 152, 255, 0.2);stop-opacity:0.6" />
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
      scrollOffset: 300,
      svgWidth: window.innerWidth,
      svgHeight: window.innerHeight,
      planets: [
        { id: 1, type: 'locked', title: '未解锁课程', position: { x: 200, y: 100 } },
        { id: 2, type: 'locked', title: '未解锁课程', position: { x: 150, y: 200 } },
        { id: 3, type: 'learning', title: '学习中', position: { x: 250, y: 350 } },
        { id: 4, type: 'available', title: '待学习', position: { x: 180, y: 450 } },
        { id: 5, type: 'completed', title: '已完成', position: { x: 220, y: 600 } },
        { id: 6, type: 'completed', title: '已完成', position: { x: 160, y: 750 } },
        { id: 7, type: 'completed', title: '已完成', position: { x: 240, y: 900 } },
        { id: 8, type: 'completed', title: '已完成', position: { x: 240, y: 900 } },
        { id: 9, type: 'completed', title: '已完成', position: { x: 240, y: 900 } },
        { id: 11, type: 'locked', title: '未解锁课程', position: { x: 200, y: 100 } },
        { id: 22, type: 'locked', title: '未解锁课程', position: { x: 150, y: 200 } },
        { id: 33, type: 'learning', title: '学习中', position: { x: 250, y: 350 } },
        { id: 44, type: 'available', title: '待学习', position: { x: 180, y: 450 } },
        { id: 55, type: 'completed', title: '已完成', position: { x: 220, y: 600 } },
        { id: 66, type: 'completed', title: '已完成', position: { x: 160, y: 750 } },
        { id: 77, type: 'completed', title: '已完成', position: { x: 240, y: 900 } },
        { id: 88, type: 'completed', title: '已完成', position: { x: 240, y: 900 } },
        { id: 99, type: 'completed', title: '已完成', position: { x: 240, y: 900 } },     
      ]
    }
  },
  computed: {
    curvePath() {
      const width = this.svgWidth
      const height = this.svgHeight
      const centerX = width / 2

      // 双S形参数
      const startY = -100
      const endY = height + 60
      const sectionHeight = (endY - startY) / 2
      const controlOffsetX = 330

      // 第一段S形
      const p0 = { x: centerX, y: startY }
      const p1 = { x: centerX + controlOffsetX, y: startY + sectionHeight / 2 }
      const p2 = { x: centerX - controlOffsetX, y: startY + sectionHeight / 2 }
      const p3 = { x: centerX, y: startY + sectionHeight }

      // 第二段S形
      const p4 = { x: centerX + controlOffsetX, y: startY + sectionHeight + sectionHeight / 2 }
      const p5 = { x: centerX - controlOffsetX, y: startY + sectionHeight + sectionHeight / 2 }
      const p6 = { x: centerX, y: endY }

      // 拼接两段贝塞尔曲线
      return `M ${p0.x} ${p0.y}
              C ${p1.x} ${p1.y}, ${p2.x} ${p2.y}, ${p3.x} ${p3.y}
              C ${p4.x} ${p4.y}, ${p5.x} ${p5.y}, ${p6.x} ${p6.y}`
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
      const maxScroll = 200
      const minScroll = -1000
      
      // 限制滚动速度，让球按路径平滑移动
      const scrollSpeed = Math.min(Math.abs(delta), 50) * Math.sign(delta)
      this.scrollOffset -= scrollSpeed * 0.3
      this.scrollOffset = Math.max(minScroll, Math.min(maxScroll, this.scrollOffset))
    },
    
    updatePlanetPositions() {
      const centerX = window.innerWidth / 2
      this.svgWidth = window.innerWidth
      
      this.planets.forEach((planet, index) => {
        // 调整进度计算，让球分布更分散，增加纵向间距
        const progress = (index * 1.5) / (this.planets.length - 1)
        const clampedProgress = Math.min(progress, 1)
        const position = this.getPointOnCurve(clampedProgress)
        
        planet.position = {
          x: centerX + position.x,
          y: position.y
        }
      })
    },

    getPointOnCurve(t) {
      // 创建临时SVG元素来计算路径上的点
      const svg = document.createElementNS('http://www.w3.org/2000/svg', 'svg')
      const path = document.createElementNS('http://www.w3.org/2000/svg', 'path')
      path.setAttribute('d', this.curvePath)
      svg.appendChild(path)
      document.body.appendChild(svg)
      
      // 获取路径总长度并计算指定比例处的点
      const totalLength = path.getTotalLength()
      const point = path.getPointAtLength(t * totalLength)
      
      // 清理临时元素
      document.body.removeChild(svg)
      
      // 返回相对于中心的坐标
      const centerX = this.svgWidth / 2
      return {
        x: point.x - centerX,
        y: point.y
      }
    },

    getScrollAdjustedPosition(originalPosition, index) {
      const baseProgress = (index * 1.5) / (this.planets.length - 1)
      const progress = baseProgress + (this.scrollOffset / 1000)
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
  /* left: 50%;
  transform: translateX(-50%); */
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