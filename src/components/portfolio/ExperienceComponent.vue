<template>
  <div class="section-content">
    <div class="timeline">
      <div class="timeline-item">
        <div class="timeline-date">May 2025 - Present</div>
        <div class="timeline-content">
          <h4>Distributed Systems Engineer</h4>
          <p class="company">Shoptaki</p>
          <p>Lead development of SmartChain technology. Mentored junior developers and implemented best practices.</p>
        </div>
      </div>
      <div class="timeline-item">
        <div class="timeline-date">December 2024 - Present</div>
        <div class="timeline-content">
          <h4>Computer Science Tutor</h4>
          <p class="company">Varsity Tutors</p>
          <p>Mentored many students of different demographics on Computer Science. Guided hands-on projects to cover topics like data structures, object-oriented programming, data analytics, etc.</p>
        </div>
      </div>
      <div class="timeline-item">
        <div class="timeline-date">May 2024 - Present</div>
        <div class="timeline-content">
          <h4>Store Associate</h4>
          <p class="company">Avis Car Rental</p>
          <p>Organized and managed a store, delivered cars in a timely manner. Regularly upkept the database to keep track of available cars.</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ExperienceComponent'
}
</script>

<style scoped>
.section-content {
  line-height: 1.6;
}

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
</style>