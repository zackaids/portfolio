<template>
  <div class="network-background">
    <!-- Background Canvas for Network Animation -->
    <canvas ref="canvas" class="network-canvas" @mousemove="handleMouseMove"></canvas>
  </div>
</template>

<script>
export default {
  name: 'NetworkBackground',
  data() {
    return {
      canvas: null,
      ctx: null,
      particles: [],
      animationId: null,
      mouse: { x: 0, y: 0 },
    }
  },
  mounted() {
    this.initCanvas()
    this.createParticles()
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
      // change the last val. for more connections
      const particleCount = Math.floor((window.innerWidth * window.innerHeight) / 16000)

      for (let i = 0; i < particleCount; i++) {
        this.particles.push({
          x: Math.random() * this.canvas.width,
          y: Math.random() * this.canvas.height,
          vx: (Math.random() - 0.5) * 0.8,
          vy: (Math.random() - 0.5) * 0.8,
          radius: Math.random() * 2.5 + 1,
          opacity: Math.random() * 0.6 + 0.2,
        })
      }
    },

    animate() {
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height)

      // particle position stuff
      this.particles.forEach((particle) => {
        particle.x += particle.vx
        particle.y += particle.vy

        // bounce params.
        if (particle.x < 0 || particle.x > this.canvas.width) particle.vx *= -1
        if (particle.y < 0 || particle.y > this.canvas.height) particle.vy *= -1

        // bounds
        particle.x = Math.max(0, Math.min(this.canvas.width, particle.x))
        particle.y = Math.max(0, Math.min(this.canvas.height, particle.y))
      })

      // connections first
      this.drawConnections()

      // particles over connections
      this.drawParticles()

      this.animationId = requestAnimationFrame(this.animate)
    },

    drawParticles() {
      this.particles.forEach((particle) => {
        this.ctx.beginPath()
        this.ctx.arc(particle.x, particle.y, particle.radius, 0, Math.PI * 2)
        this.ctx.fillStyle = `rgba(100, 200, 255, ${particle.opacity})`
        this.ctx.fill()

        // glow effect
        this.ctx.shadowColor = 'rgba(100, 200, 255, 0.5)'
        this.ctx.shadowBlur = 10
        this.ctx.fill()
        this.ctx.shadowBlur = 0
      })
    },

    drawConnections() {
      const maxDistance = 150

      // connection between particles
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

        // drawing connection to cursor... working yay
        const mouseDistance = Math.sqrt(
          (this.particles[i].x - this.mouse.x) ** 2 + (this.particles[i].y - this.mouse.y) ** 2,
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
    },

    handleMouseMove(event) {
      this.mouse.x = event.clientX
      this.mouse.y = event.clientY
      console.log(event.clientX, event.clientY)
    },

    handleResize() {
      this.resizeCanvas()
      this.createParticles()
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
  cursor: none;
}
</style>
