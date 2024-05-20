<script>
export default {
  name: 'ShimmerLoader',
  props: {
    loading: {
      type: Boolean,
      required: true
    }
  }
}
</script>

<template>
  <div v-if="loading" class="character-cards_wrapper">
    <div v-for="i in 6" :key="i" class="loader-card shimmerBG">
      <div class="content-line"></div>
    </div>
  </div>
</template>

<style scoped>
.loader-card {
  position: relative;
  background-color: var(--base-color);
  height: 300px;
  margin: 46px 0;
  overflow: hidden;
  width: 300px;
  .content-line {
    width: 150px;
    height: 30px;
    padding: 0 10px;
    background-color: #fff;
    position: absolute;
    top: 83%;
  }
}
@keyframes shimmer {
  0% {
    background-position: -1200px 0;
  }
  100% {
    background-position: 1200px 0;
  }
}
.shimmerBG {
  animation: shimmer 2.5s forwards infinite linear;
  background: linear-gradient(to right, var(--base-color) 8%, #fffffd 18%, var(--base-color) 33%);
  background-size: 1200px 100%;
}
</style>
