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
import { onBeforeUnmount } from 'vue'
export default {
  setup(props, { emit }) {
    let onKeydown = event => {
      if (event.key == 'Escape') closeModal()
    }

    function closeModal() {
      emit('closeModal')
    }

    window.addEventListener('keydown', onKeydown)

    onBeforeUnmount(() => {
      window.removeEventListener('keydown', onKeydown)
    })
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
