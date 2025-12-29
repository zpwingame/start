<template>
  <div class="knowledge-galaxy">
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

    <div class="planets-container" :style="{ height: svgHeight + 'px' }">
      <PlanetComponent
        v-for="(planet, index) in planets"
        :key="planet.id"
        :planet="planet"
        :position="planet.position"
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
      svgWidth: window.innerWidth,
      planets: [
        { id: 1, type: 'locked', title: '未解锁课程', position: { x: 0, y: 0 } },
        { id: 2, type: 'locked', title: '未解锁课程', position: { x: 0, y: 0 } },
        { id: 3, type: 'learning', title: '学习中', position: { x: 0, y: 0 } },
        { id: 4, type: 'available', title: '待学习', position: { x: 0, y: 0 } },
        { id: 5, type: 'completed', title: '已完成', position: { x: 0, y: 0 } },
        { id: 6, type: 'completed', title: '已完成', position: { x: 0, y: 0 } },
        { id: 7, type: 'completed', title: '已完成', position: { x: 0, y: 0 } },
        { id: 8, type: 'completed', title: '已完成', position: { x: 0, y: 0 } },
        { id: 9, type: 'completed', title: '已完成', position: { x: 0, y: 0 } },
        { id: 11, type: 'locked', title: '未解锁课程', position: { x: 0, y: 0 } },
        { id: 22, type: 'locked', title: '未解锁课程', position: { x: 0, y: 0 } },
        { id: 33, type: 'learning', title: '学习中', position: { x: 0, y: 0 } },
        { id: 44, type: 'available', title: '待学习', position: { x: 0, y: 0 } },
        { id: 55, type: 'completed', title: '已完成', position: { x: 0, y: 0 } },
        { id: 66, type: 'completed', title: '已完成', position: { x: 0, y: 0 } },
        { id: 77, type: 'completed', title: '已完成', position: { x: 0, y: 0 } },
        { id: 88, type: 'completed', title: '已完成', position: { x: 0, y: 0 } },
        { id: 99, type: 'completed', title: '已完成', position: { x: 0, y: 0 } },
      ]
    }
  },
  computed: {
    svgHeight() {
      // 根据球的数量计算SVG高度：球数量 / 5 * 100vh
      const ballsPerViewport = 5
      const viewportHeight = window.innerHeight
      return Math.ceil(this.planets.length / ballsPerViewport) * viewportHeight
    },
    curvePath() {
      const width = this.svgWidth
      const height = this.svgHeight
      const centerX = width / 2

      // 根据高度计算需要多少段S曲线
      const viewportHeight = window.innerHeight
      const numSegments = Math.ceil(height / viewportHeight)

      // S曲线参数
      const segmentHeight = height / numSegments
      const controlOffsetX = 330

      let path = ''

      for (let i = 0; i < numSegments; i++) {
        const startY = i * segmentHeight
        const endY = (i + 1) * segmentHeight
        const sectionHeight = endY - startY

        if (i === 0) {
          // 第一段，用 M 开始
          const p0 = { x: centerX, y: startY }
          const p1 = { x: centerX + controlOffsetX, y: startY + sectionHeight / 2 }
          const p2 = { x: centerX - controlOffsetX, y: startY + sectionHeight / 2 }
          const p3 = { x: centerX, y: endY }

          path = `M ${p0.x} ${p0.y} C ${p1.x} ${p1.y}, ${p2.x} ${p2.y}, ${p3.x} ${p3.y}`
        } else {
          // 后续段，用 C 连接（方向交替）
          const isEven = i % 2 === 0
          const offset1 = isEven ? controlOffsetX : -controlOffsetX
          const offset2 = isEven ? -controlOffsetX : controlOffsetX

          const p1 = { x: centerX + offset1, y: startY + sectionHeight / 2 }
          const p2 = { x: centerX + offset2, y: startY + sectionHeight / 2 }
          const p3 = { x: centerX, y: endY }

          path += ` C ${p1.x} ${p1.y}, ${p2.x} ${p2.y}, ${p3.x} ${p3.y}`
        }
      }

      return path
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
    updatePlanetPositions() {
      this.svgWidth = window.innerWidth

      // 球均匀分布在整个曲线上
      this.planets.forEach((planet, index) => {
        const progress = index / (this.planets.length - 1) // 从0到1均匀分布
        const position = this.getPointOnCurve(progress)

        planet.position = {
          x: position.x,
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

      return {
        x: point.x,
        y: point.y
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
  overflow-y: auto;
  z-index: 10;
}

.curve-path {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 5;
  filter: url(#glow);
}

.planets-container {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 15;
}

.main-curve {
  filter: drop-shadow(0 0 8px rgba(135, 206, 235, 0.6));
}
</style>