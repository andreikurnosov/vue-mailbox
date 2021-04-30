<template>
  <div class="modal">
    <div class="overlay" @click="closeModal"></div>
    <div class="modal-card">
      <slot />
      <button @click="closeModal" class="close-modal">❌</button>
    </div>
  </div>
</template>

<script>
import useKeydown from '../composables/use-keydown.js'

export default {
  setup(props, { emit }) {
    useKeydown([{ key: 'Escape', fn: closeModal }])

    function closeModal() {
      emit('closeModal')
    }
    return {
      closeModal
    }
  }
}
</script>

<style>
.close-modal {
  position: absolute;
  top: -8px;
  right: -8px;
  background: transparent;
  border: 0;
}
</style>
