<template>
  <div class="section-content">
    <div class="contact-info">
      <div class="contact-item">
        <span class="contact-icon">📧</span>
        <div>
          <h4>Email</h4>
          <p>zaidarov2004@gmail.com</p>
        </div>
      </div>
      <div class="contact-item">
        <span class="contact-icon">🐙</span>
        <div>
          <h4>GitHub</h4>
          <p>https://github.com/zackaids</p>
        </div>
      </div>
      <div class="contact-item">
        <span class="contact-icon">💼</span>
        <div>
          <h4>LinkedIn</h4>
          <p>https://www.linkedin.com/in/zalkar-aidarov/</p>
        </div>
      </div>
      <div class="contact-item">
        <span class="contact-icon">🌐</span>
        <div>
          <h4>Website</h4>
          <p>zackaids.dev</p>
        </div>
      </div>
    </div>
    <div class="contact-form">
      <h4>Send a Message</h4>
      <form @submit.prevent="handleSubmit">
        <input v-model="form.name" type="text" placeholder="Your Name" class="form-input">
        <input v-model="form.email" type="email" placeholder="Your Email" class="form-input">
        <textarea v-model="form.message" placeholder="Your Message" class="form-textarea"></textarea>
        <button type="submit" class="form-button">Send Message</button>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ContactComponent',
  data() {
    return {
      form: {
        name: '',
        email: '',
        message: ''
      }
    }
  },
  methods: {
    handleSubmit() {
      console.log('Form submitted:', this.form)
      // TODO: actual form submission logic
      alert('Message sent! (This is just a demo)')
      
      // Reset form
      this.form = {
        name: '',
        email: '',
        message: ''
      }
    }
  }
}
</script>

<style scoped>
.section-content {
  line-height: 1.6;
}

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
  box-sizing: border-box;
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