<template>
  <div class="modal">
    <div class="overlay" @click="closeModal"></div>
    <div class="modal-card">
      <slot />
      <button @click="closeModal" class="close-modal">❌ </button>
    </div>
  </div>
</template>

<script>
export default {
  setup(props, { emit }) {
    function closeModal() {
      emit('closeModal')
    }

    return {
      closeModal
    }
  }
}
</script>

<style >
.close-modal {
  position: absolute;
  top: -8px;
  right: -8px;
  background: transparent;
  border: 0;
}
</style>
