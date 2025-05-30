<template>
  <div class="section-content">
    <div class="profile-section">
      <div class="avatar-placeholder"></div>
      <h3>Zack Aidarov</h3>
      <p class="title">Data Scientist | Distributed Systems Engineer | Computer Science Tutor</p>
    </div>
    <p>Description soon</p>
    <div class="skills">
      <span class="skill-tag">Vue.js</span>
      <span class="skill-tag">React</span>
      <span class="skill-tag">Node.js</span>
      <span class="skill-tag">Python</span>
      <span class="skill-tag">TypeScript</span>
      <span class="skill-tag">C/C++</span>
      <span class="skill-tag">Java</span>
      <span class="skill-tag">SQL</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'AboutComponent'
}
</script>

<style scoped>
.section-content {
  line-height: 1.6;
}

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
</style>