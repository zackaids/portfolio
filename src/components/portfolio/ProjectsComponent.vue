<template>
  <div class="section-content">
    <div class="project-grid">
      <div class="project-card">
        <h4>Walmart Effect</h4>
        <p>Data Science study that documents the effects that Walmart has on rural counties</p>
        <div class="project-tags">
          <span>Python</span>
          <span>SQL</span>
          <span>Pandas</span>
          <span>Matplotlib</span>
          <span>Scikit-learn</span>
        </div>
      </div>
      <div class="project-card">
        <h4>Kiwi Math</h4>
        <p>React and Flask web app leveraging AI to help high school students with math</p>
        <div class="project-tags">
          <span>React</span>
          <span>MongoDB</span>
          <span>Flask</span>
        </div>
      </div>
      <div class="project-card">
        <h4>HireHive</h4>
        <p>Full-stack web app that helps automate resume screening for businesses</p>
        <div class="project-tags">
          <span>React.js</span>
          <span>Flask</span>
          <span>MongoDB</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProjectsComponent'
}
</script>

<style scoped>
.section-content {
  line-height: 1.6;
}

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
  flex-wrap: wrap;
}

.project-tags span {
  background: rgba(100, 255, 100, 0.2);
  color: #64ff64;
  padding: 3px 8px;
  border-radius: 12px;
  font-size: 11px;
}
</style>