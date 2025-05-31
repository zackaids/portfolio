<template>
  <div class="network-background">
    <!-- Background Canvas for Network Animation -->
    <canvas ref="canvas" class="network-canvas" @mousemove="handleMouseMove" @click="handleClick"></canvas>
    
    <!-- Information Modal -->
    <div v-if="selectedNode" class="modal-overlay" @click="closeModal">
      <div class="modal-content" @click.stop>
        <button class="close-btn" @click="closeModal">&times;</button>
        <h2>{{ selectedNode.label }}</h2>
        <div class="modal-body">
          <component :is="selectedNode.component" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import AboutComponent from './portfolio/AboutComponent.vue'
import ProjectsComponent from './portfolio/ProjectsComponent.vue'
import ExperienceComponent from './portfolio/ExperienceComponent.vue'
import ContactComponent from './portfolio/ContactComponent.vue'

export default {
  name: 'NetworkBackground',
  components: {
    AboutComponent,
    ProjectsComponent,
    ExperienceComponent,
    ContactComponent
  },
  data() {
    return {
      canvas: null,
      ctx: null,
      particles: [],
      portfolioNodes: [],
      animationId: null,
      mouse: { x: 0, y: 0 },
      selectedNode: null,
      hoveredNode: null,
    }
  },
  mounted() {
    this.initCanvas()
    this.createParticles()
    this.createPortfolioNodes()
    this.animate()
    window.addEventListener('resize', this.handleResize)
  },
  beforeUnmount() {
    if (this.animationId) {
      cancelAnimationFrame(this.animationId)
    }
    window.removeEventListener('resize', this.handleResize)
  },
  methods: {
    initCanvas() {
      this.canvas = this.$refs.canvas
      this.ctx = this.canvas.getContext('2d')
      this.resizeCanvas()
    },

    resizeCanvas() {
      this.canvas.width = window.innerWidth
      this.canvas.height = window.innerHeight
    },

    createParticles() {
      this.particles = []
      const particleCount = Math.floor((window.innerWidth * window.innerHeight) / 8000)

      for (let i = 0; i < particleCount; i++) {
        this.particles.push({
          x: Math.random() * this.canvas.width,
          y: Math.random() * this.canvas.height,
          vx: (Math.random() - 0.5) * 0.8,
          vy: (Math.random() - 0.5) * 0.8,
          radius: Math.random() * 2.5 + 1,
          opacity: Math.random() * 0.6 + 0.2,
          isPortfolio: false
        })
      }
    },

    createPortfolioNodes() {
      const sections = [
        { id: 'about', label: 'About', component: 'AboutComponent', color: [255, 100, 100] },
        { id: 'projects', label: 'Projects', component: 'ProjectsComponent', color: [100, 255, 100]},
        { id: 'experience', label: 'Experience', component: 'ExperienceComponent', color: [100, 100, 255]},
        { id: 'contact', label: 'Contact', component: 'ContactComponent', color: [255, 255, 100]}
      ]

      // Position nodes in different quadrants with orbital centers
      const centerX = this.canvas.width / 2
      const centerY = this.canvas.height / 2
      const orbitalRadius = Math.min(centerX, centerY) * 0.4

      sections.forEach((section, index) => {
        const angle = (index / sections.length) * Math.PI * 2
        const orbitalCenterX = centerX + Math.cos(angle) * orbitalRadius
        const orbitalCenterY = centerY + Math.sin(angle) * orbitalRadius

        const portfolioNode = {
          ...section,
          x: orbitalCenterX,
          y: orbitalCenterY,
          orbitalCenterX,
          orbitalCenterY,
          orbitalRadius: 60 + Math.random() * 40, // Random orbital distance
          orbitalSpeed: 0.005 + Math.random() * 0.01, // Random orbital speed
          orbitalAngle: Math.random() * Math.PI * 2, // Random starting angle
          baseRadius: 20,
          currentRadius: 20,
          targetRadius: 20,
          hovered: false,
          pulsePhase: Math.random() * Math.PI * 2,
        }

        this.portfolioNodes.push(portfolioNode)
      })
    },

    animate() {
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height)

      // Update portfolio nodes with orbital motion
      this.portfolioNodes.forEach(node => {
        // Update orbital position
        node.orbitalAngle += node.orbitalSpeed
        
        // Calculate new position with some randomness
        const randomOffset = Math.sin(node.pulsePhase) * 10
        node.x = node.orbitalCenterX + Math.cos(node.orbitalAngle) * (node.orbitalRadius + randomOffset)
        node.y = node.orbitalCenterY + Math.sin(node.orbitalAngle) * (node.orbitalRadius + randomOffset * 0.5)
        
        // Update pulse phase
        node.pulsePhase += 0.03

        // Smooth radius transition for hover effect
        node.currentRadius += (node.targetRadius - node.currentRadius) * 0.1
      })

      // Update regular particles
      this.particles.forEach((particle) => {
        particle.x += particle.vx
        particle.y += particle.vy

        if (particle.x < 0 || particle.x > this.canvas.width) particle.vx *= -1
        if (particle.y < 0 || particle.y > this.canvas.height) particle.vy *= -1

        particle.x = Math.max(0, Math.min(this.canvas.width, particle.x))
        particle.y = Math.max(0, Math.min(this.canvas.height, particle.y))
      })

      this.drawConnections()
      this.drawParticles()
      this.drawPortfolioNodes()

      this.animationId = requestAnimationFrame(this.animate)
    },

    drawParticles() {
      this.particles.forEach((particle) => {
        this.ctx.beginPath()
        this.ctx.arc(particle.x, particle.y, particle.radius, 0, Math.PI * 2)
        
        this.ctx.fillStyle = `rgba(100, 200, 255, ${particle.opacity})`
        this.ctx.fill()

        this.ctx.shadowColor = 'rgba(100, 200, 255, 0.5)'
        this.ctx.shadowBlur = 10
        this.ctx.fill()
        this.ctx.shadowBlur = 0
      })
    },

    drawPortfolioNodes() {
      this.portfolioNodes.forEach(node => {
        const [r, g, b] = node.color
        const pulseIntensity = 0.4 + 0.3 * Math.sin(node.pulsePhase)
        
        // Draw outer glow
        this.ctx.beginPath()
        this.ctx.arc(node.x, node.y, node.currentRadius + 10, 0, Math.PI * 2)
        this.ctx.fillStyle = `rgba(${r}, ${g}, ${b}, ${pulseIntensity * 0.2})`
        this.ctx.fill()

        // Draw main node
        this.ctx.beginPath()
        this.ctx.arc(node.x, node.y, node.currentRadius, 0, Math.PI * 2)
        this.ctx.fillStyle = `rgba(${r}, ${g}, ${b}, 0.8)`
        this.ctx.fill()

        // Draw inner highlight
        this.ctx.beginPath()
        this.ctx.arc(node.x - node.currentRadius * 0.3, node.y - node.currentRadius * 0.3, node.currentRadius * 0.4, 0, Math.PI * 2)
        this.ctx.fillStyle = `rgba(255, 255, 255, 0.4)`
        this.ctx.fill()

        // Draw border
        this.ctx.beginPath()
        this.ctx.arc(node.x, node.y, node.currentRadius, 0, Math.PI * 2)
        this.ctx.strokeStyle = `rgba(${r}, ${g}, ${b}, 1)`
        this.ctx.lineWidth = 2
        this.ctx.stroke()

        // Draw label if hovered
        if (node.hovered) {
          this.ctx.fillStyle = 'white'
          this.ctx.font = 'bold 16px Arial'
          this.ctx.textAlign = 'center'
          this.ctx.textBaseline = 'middle'
          
          // Text shadow
          this.ctx.shadowColor = 'rgba(0, 0, 0, 0.8)'
          this.ctx.shadowBlur = 4
          this.ctx.fillText(node.label, node.x, node.y - node.currentRadius - 25)
          this.ctx.shadowBlur = 0
        }
      })
    },

    drawConnections() {
      const maxDistance = 150

      // Connections between particles
      for (let i = 0; i < this.particles.length; i++) {
        for (let j = i + 1; j < this.particles.length; j++) {
          const dx = this.particles[i].x - this.particles[j].x
          const dy = this.particles[i].y - this.particles[j].y
          const distance = Math.sqrt(dx * dx + dy * dy)

          if (distance < maxDistance) {
            const opacity = (1 - distance / maxDistance) * 0.4
            this.ctx.strokeStyle = `rgba(100, 200, 255, ${opacity})`
            this.ctx.lineWidth = 0.8
            this.ctx.beginPath()
            this.ctx.moveTo(this.particles[i].x, this.particles[i].y)
            this.ctx.lineTo(this.particles[j].x, this.particles[j].y)
            this.ctx.stroke()
          }
        }

        // Mouse connections to particles
        const mouseDistance = Math.sqrt(
          (this.particles[i].x - this.mouse.x) ** 2 + (this.particles[i].y - this.mouse.y) ** 2
        )

        if (mouseDistance < 120) {
          const opacity = (1 - mouseDistance / 120) * 0.7
          this.ctx.strokeStyle = `rgba(255, 150, 100, ${opacity})`
          this.ctx.lineWidth = 1.5
          this.ctx.beginPath()
          this.ctx.moveTo(this.particles[i].x, this.particles[i].y)
          this.ctx.lineTo(this.mouse.x, this.mouse.y)
          this.ctx.stroke()
        }
      }

      // Connections between portfolio nodes and particles
      this.portfolioNodes.forEach(node => {
        this.particles.forEach(particle => {
          const dx = node.x - particle.x
          const dy = node.y - particle.y
          const distance = Math.sqrt(dx * dx + dy * dy)

          if (distance < maxDistance) {
            const opacity = (1 - distance / maxDistance) * 0.6
            const [r, g, b] = node.color
            this.ctx.strokeStyle = `rgba(${r}, ${g}, ${b}, ${opacity})`
            this.ctx.lineWidth = 1.2
            this.ctx.beginPath()
            this.ctx.moveTo(node.x, node.y)
            this.ctx.lineTo(particle.x, particle.y)
            this.ctx.stroke()
          }
        })

        // Mouse connections to portfolio nodes
        const mouseDistance = Math.sqrt(
          (node.x - this.mouse.x) ** 2 + (node.y - this.mouse.y) ** 2
        )

        if (mouseDistance < 150) {
          const opacity = (1 - mouseDistance / 150) * 0.8
          const [r, g, b] = node.color
          this.ctx.strokeStyle = `rgba(${r}, ${g}, ${b}, ${opacity})`
          this.ctx.lineWidth = 2
          this.ctx.beginPath()
          this.ctx.moveTo(node.x, node.y)
          this.ctx.lineTo(this.mouse.x, this.mouse.y)
          this.ctx.stroke()
        }
      })
    },

    handleMouseMove(event) {
      this.mouse.x = event.clientX
      this.mouse.y = event.clientY

      // Check if hovering over portfolio nodes
      this.hoveredNode = null
      this.portfolioNodes.forEach(node => {
        const distance = Math.sqrt(
          (node.x - this.mouse.x) ** 2 + (node.y - this.mouse.y) ** 2
        )
        const isHovered = distance < node.currentRadius + 10
        node.hovered = isHovered
        node.targetRadius = isHovered ? node.baseRadius * 1.5 : node.baseRadius
        
        if (isHovered) {
          this.hoveredNode = node
        }
      })

      // Update cursor style
      this.canvas.style.cursor = this.hoveredNode ? 'pointer' : 'default'
    },

    handleClick(event) {
      const clickX = event.clientX
      const clickY = event.clientY

      // Check if clicked on a portfolio node
      for (const node of this.portfolioNodes) {
        const distance = Math.sqrt(
          (node.x - clickX) ** 2 + (node.y - clickY) ** 2
        )
        if (distance < node.currentRadius + 10) {
          this.selectedNode = node
          break
        }
      }
    },

    closeModal() {
      this.selectedNode = null
    },

    handleResize() {
      this.resizeCanvas()
      this.createParticles()
      this.createPortfolioNodes()
    },
  },
}
</script>

<style scoped>
.network-background {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background: linear-gradient(135deg, #0a0a0a 0%, #1a1a1a 50%, #0f0f0f 100%);
  z-index: -1;
}

.network-canvas {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  backdrop-filter: blur(5px);
}

.modal-content {
  background: linear-gradient(135deg, #1a1a1a 0%, #2a2a2a 100%);
  border-radius: 15px;
  padding: 30px;
  max-width: 800px;
  max-height: 80vh;
  overflow-y: auto;
  color: white;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5);
  border: 1px solid rgba(255, 255, 255, 0.1);
  position: relative;
}

.close-btn {
  position: absolute;
  top: 15px;
  right: 20px;
  background: none;
  border: none;
  color: white;
  font-size: 24px;
  cursor: pointer;
  opacity: 0.7;
  transition: opacity 0.3s ease;
}

.close-btn:hover {
  opacity: 1;
}

.modal-content h2 {
  margin-top: 0;
  margin-bottom: 20px;
  font-size: 28px;
  background: linear-gradient(45deg, #64c8ff, #ff6464);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
</style>