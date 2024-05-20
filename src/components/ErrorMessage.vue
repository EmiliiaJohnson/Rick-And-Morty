<script>
export default {
  name: 'ErrorMessage',
  props: {
    error: {
      type: Boolean,
      required: true
    }
  }
}
</script>

<template>
  <div v-if="error" class="error-message">
    Error <br />
    No character found
  </div>
</template>

<style scoped>
.error-message {
  text-align: center;
  margin: auto;
  font-size: 4em;
  color: #ffde00;
}
</style>
