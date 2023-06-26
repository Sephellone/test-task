<script setup>
  import ProgressBar from './components/ProgressBar.vue'
</script>

<template>
  <main>
    <div class="container">
      <h2 class="container__title">Target Indicator Demo</h2>
      <div class="container__body">
        <ProgressBar></ProgressBar>
      </div>
    </div>
    
  </main>
</template>

<style scoped>
.container {
  width: 450px;
  margin: auto;
  box-shadow: 0 0 10px var(--base-gray);
  border: 1px solid var(--base-gray);
  border-radius: 10px;
  overflow: hidden;
}

.container__title {
  padding: 8px 15px;
  font-weight: 500;
  font-size: 16px;
  background-color: var(--base-gray);
  color: white;
}

.container__body {
  padding: 15px;
  background-color: var(--light-gray);
}
</style>
