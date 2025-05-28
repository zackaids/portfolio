<template>
  <div class="network-background">
    <!-- Background Canvas for Network Animation -->
    <canvas ref="canvas" class="network-canvas" @mousemove="handleMouseMove" @click="handleClick"></canvas>
    
    <!-- Portfolio Node Labels -->
    <div v-for="node in portfolioNodes" :key="node.id" 
         class="node-label"
         :style="{ 
           left: node.x + 'px', 
           top: node.y + 'px',
           opacity: node.hovered ? 1 : 0.8
         }">
      {{ node.label }}
    </div>

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
// Portfolio Section Components
const AboutComponent = {
  template: `
    <div class="section-content">
      <div class="profile-section">
        <div class="avatar-placeholder"></div>
        <h3>John Doe</h3>
        <p class="title">Full Stack Developer</p>
      </div>
      <p>Passionate developer with 5+ years of experience creating innovative web applications. 
         I specialize in Vue.js, Node.js, and modern web technologies.</p>
      <div class="skills">
        <span class="skill-tag">Vue.js</span>
        <span class="skill-tag">React</span>
        <span class="skill-tag">Node.js</span>
        <span class="skill-tag">Python</span>
        <span class="skill-tag">TypeScript</span>
      </div>
    </div>
  `
}

const ProjectsComponent = {
  template: `
    <div class="section-content">
      <div class="project-grid">
        <div class="project-card">
          <h4>E-Commerce Platform</h4>
          <p>Full-stack web application built with Vue.js and Node.js</p>
          <div class="project-tags">
            <span>Vue.js</span>
            <span>Express</span>
            <span>MongoDB</span>
          </div>
        </div>
        <div class="project-card">
          <h4>Data Visualization Dashboard</h4>
          <p>Interactive dashboard for analytics and reporting</p>
          <div class="project-tags">
            <span>React</span>
            <span>D3.js</span>
            <span>Python</span>
          </div>
        </div>
        <div class="project-card">
          <h4>Mobile Weather App</h4>
          <p>Cross-platform mobile application with real-time data</p>
          <div class="project-tags">
            <span>React Native</span>
            <span>API Integration</span>
          </div>
        </div>
      </div>
    </div>
  `
}

const ExperienceComponent = {
  template: `
    <div class="section-content">
      <div class="timeline">
        <div class="timeline-item">
          <div class="timeline-date">2022 - Present</div>
          <div class="timeline-content">
            <h4>Senior Frontend Developer</h4>
            <p class="company">Tech Innovations Inc.</p>
            <p>Lead development of client-facing applications using Vue.js and React. Mentored junior developers and implemented best practices.</p>
          </div>
        </div>
        <div class="timeline-item">
          <div class="timeline-date">2020 - 2022</div>
          <div class="timeline-content">
            <h4>Full Stack Developer</h4>
            <p class="company">StartupXYZ</p>
            <p>Built and maintained web applications from ground up. Worked with cross-functional teams to deliver high-quality products.</p>
          </div>
        </div>
        <div class="timeline-item">
          <div class="timeline-date">2019 - 2020</div>
          <div class="timeline-content">
            <h4>Junior Developer</h4>
            <p class="company">Web Solutions Co.</p>
            <p>Developed responsive websites and learned modern development practices. Gained experience in agile methodologies.</p>
          </div>
        </div>
      </div>
    </div>
  `
}

const ContactComponent = {
  template: `
    <div class="section-content">
      <div class="contact-info">
        <div class="contact-item">
          <span class="contact-icon">📧</span>
          <div>
            <h4>Email</h4>
            <p>john.doe@example.com</p>
          </div>
        </div>
        <div class="contact-item">
          <span class="contact-icon">🐙</span>
          <div>
            <h4>GitHub</h4>
            <p>github.com/johndoe</p>
          </div>
        </div>
        <div class="contact-item">
          <span class="contact-icon">💼</span>
          <div>
            <h4>LinkedIn</h4>
            <p>linkedin.com/in/johndoe</p>
          </div>
        </div>
        <div class="contact-item">
          <span class="contact-icon">🌐</span>
          <div>
            <h4>Website</h4>
            <p>johndoe.dev</p>
          </div>
        </div>
      </div>
      <div class="contact-form">
        <h4>Send a Message</h4>
        <form @submit.prevent>
          <input type="text" placeholder="Your Name" class="form-input">
          <input type="email" placeholder="Your Email" class="form-input">
          <textarea placeholder="Your Message" class="form-textarea"></textarea>
          <button type="submit" class="form-button">Send Message</button>
        </form>
      </div>
    </div>
  `
}

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
      const particleCount = Math.floor((window.innerWidth * window.innerHeight) / 16000)

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
        { id: 'projects', label: 'Projects', component: 'ProjectsComponent', color: [100, 255, 100] },
        { id: 'experience', label: 'Experience', component: 'ExperienceComponent', color: [100, 100, 255] },
        { id: 'contact', label: 'Contact', component: 'ContactComponent', color: [255, 255, 100] }
      ]

      // Position nodes in a roughly circular pattern
      const centerX = this.canvas.width / 2
      const centerY = this.canvas.height / 2
      const radius = Math.min(centerX, centerY) * 0.6

      sections.forEach((section, index) => {
        const angle = (index / sections.length) * Math.PI * 2
        const x = centerX + Math.cos(angle) * radius
        const y = centerY + Math.sin(angle) * radius

        const portfolioNode = {
          ...section,
          x: x,
          y: y,
          radius: 15,
          hovered: false,
          clicked: false,
          pulsePhase: Math.random() * Math.PI * 2
        }

        this.portfolioNodes.push(portfolioNode)

        // Add corresponding particle
        this.particles.push({
          x: x,
          y: y,
          vx: 0,
          vy: 0,
          radius: 15,
          opacity: 0.9,
          isPortfolio: true,
          portfolioId: section.id,
          color: section.color,
          pulsePhase: portfolioNode.pulsePhase
        })
      })
    },

    animate() {
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height)

      // Update regular particles
      this.particles.forEach((particle) => {
        if (!particle.isPortfolio) {
          particle.x += particle.vx
          particle.y += particle.vy

          if (particle.x < 0 || particle.x > this.canvas.width) particle.vx *= -1
          if (particle.y < 0 || particle.y > this.canvas.height) particle.vy *= -1

          particle.x = Math.max(0, Math.min(this.canvas.width, particle.x))
          particle.y = Math.max(0, Math.min(this.canvas.height, particle.y))
        } else {
          // Portfolio nodes have a gentle floating motion
          particle.pulsePhase += 0.02
          particle.y += Math.sin(particle.pulsePhase) * 0.5
        }
      })

      this.drawConnections()
      this.drawParticles()

      this.animationId = requestAnimationFrame(this.animate)
    },

    drawParticles() {
      this.particles.forEach((particle) => {
        this.ctx.beginPath()
        this.ctx.arc(particle.x, particle.y, particle.radius, 0, Math.PI * 2)
        
        if (particle.isPortfolio) {
          // Portfolio nodes with special styling
          const [r, g, b] = particle.color
          const pulseIntensity = 0.3 + 0.2 * Math.sin(particle.pulsePhase)
          
          this.ctx.fillStyle = `rgba(${r}, ${g}, ${b}, ${particle.opacity})`
          this.ctx.fill()

          // Glow effect
          this.ctx.shadowColor = `rgba(${r}, ${g}, ${b}, ${pulseIntensity})`
          this.ctx.shadowBlur = 20
          this.ctx.fill()
          this.ctx.shadowBlur = 0

          // Ring effect
          this.ctx.strokeStyle = `rgba(${r}, ${g}, ${b}, 0.5)`
          this.ctx.lineWidth = 2
          this.ctx.stroke()
        } else {
          // Regular particles
          this.ctx.fillStyle = `rgba(100, 200, 255, ${particle.opacity})`
          this.ctx.fill()

          this.ctx.shadowColor = 'rgba(100, 200, 255, 0.5)'
          this.ctx.shadowBlur = 10
          this.ctx.fill()
          this.ctx.shadowBlur = 0
        }
      })
    },

    drawConnections() {
      const maxDistance = 150

      for (let i = 0; i < this.particles.length; i++) {
        for (let j = i + 1; j < this.particles.length; j++) {
          const dx = this.particles[i].x - this.particles[j].x
          const dy = this.particles[i].y - this.particles[j].y
          const distance = Math.sqrt(dx * dx + dy * dy)

          if (distance < maxDistance) {
            let opacity = (1 - distance / maxDistance) * 0.4
            let color = '100, 200, 255'

            // Special connections involving portfolio nodes
            if (this.particles[i].isPortfolio || this.particles[j].isPortfolio) {
              opacity *= 1.5
              color = '150, 255, 150'
            }

            this.ctx.strokeStyle = `rgba(${color}, ${opacity})`
            this.ctx.lineWidth = 0.8
            this.ctx.beginPath()
            this.ctx.moveTo(this.particles[i].x, this.particles[i].y)
            this.ctx.lineTo(this.particles[j].x, this.particles[j].y)
            this.ctx.stroke()
          }
        }

        // Mouse connections
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
        node.hovered = distance < 30
        if (node.hovered) {
          this.hoveredNode = node
        }
      })

      // Update cursor style
      this.canvas.style.cursor = this.hoveredNode ? 'pointer' : 'none'
    },

    handleClick(event) {
      const clickX = event.clientX
      const clickY = event.clientY

      // Check if clicked on a portfolio node
      for (const node of this.portfolioNodes) {
        const distance = Math.sqrt(
          (node.x - clickX) ** 2 + (node.y - clickY) ** 2
        )
        if (distance < 30) {
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
  cursor: none;
}

.node-label {
  position: absolute;
  color: white;
  font-size: 14px;
  font-weight: 600;
  text-shadow: 0 0 10px rgba(255, 255, 255, 0.5);
  pointer-events: none;
  transform: translate(-50%, -50%);
  transition: opacity 0.3s ease;
  z-index: 10;
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

.section-content {
  line-height: 1.6;
}

/* About Section Styles */
.profile-section {
  text-align: center;
  margin-bottom: 20px;
}

.avatar-placeholder {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  background: linear-gradient(45deg, #64c8ff, #ff6464);
  margin: 0 auto 15px;
}

.profile-section h3 {
  margin: 0;
  font-size: 24px;
}

.title {
  color: #64c8ff;
  font-weight: 600;
  margin: 5px 0 15px;
}

.skills {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  margin-top: 20px;
}

.skill-tag {
  background: rgba(100, 200, 255, 0.2);
  color: #64c8ff;
  padding: 5px 12px;
  border-radius: 20px;
  font-size: 12px;
  border: 1px solid rgba(100, 200, 255, 0.3);
}

/* Projects Section Styles */
.project-grid {
  display: grid;
  gap: 20px;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
}

.project-card {
  background: rgba(255, 255, 255, 0.05);
  padding: 20px;
  border-radius: 10px;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.project-card h4 {
  margin-top: 0;
  color: #64c8ff;
}

.project-tags {
  display: flex;
  gap: 8px;
  margin-top: 15px;
}

.project-tags span {
  background: rgba(100, 255, 100, 0.2);
  color: #64ff64;
  padding: 3px 8px;
  border-radius: 12px;
  font-size: 11px;
}

/* Experience Section Styles */
.timeline {
  position: relative;
}

.timeline-item {
  display: flex;
  margin-bottom: 25px;
  gap: 20px;
}

.timeline-date {
  min-width: 120px;
  color: #64c8ff;
  font-weight: 600;
  font-size: 14px;
}

.timeline-content h4 {
  margin: 0 0 5px;
  color: white;
}

.company {
  color: #64ff64;
  font-weight: 600;
  margin: 0 0 10px;
}

/* Contact Section Styles */
.contact-info {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 20px;
  margin-bottom: 30px;
}

.contact-item {
  display: flex;
  align-items: center;
  gap: 15px;
}

.contact-icon {
  font-size: 24px;
}

.contact-item h4 {
  margin: 0 0 5px;
  color: #64c8ff;
}

.contact-item p {
  margin: 0;
  opacity: 0.8;
}

.contact-form {
  background: rgba(255, 255, 255, 0.05);
  padding: 20px;
  border-radius: 10px;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.form-input, .form-textarea {
  width: 100%;
  padding: 12px;
  margin-bottom: 15px;
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
  border-radius: 8px;
  color: white;
  font-family: inherit;
}

.form-input::placeholder, .form-textarea::placeholder {
  color: rgba(255, 255, 255, 0.5);
}

.form-textarea {
  height: 100px;
  resize: vertical;
}

.form-button {
  background: linear-gradient(45deg, #64c8ff, #ff6464);
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 600;
  transition: transform 0.2s ease;
}

.form-button:hover {
  transform: translateY(-2px);
}
</style>