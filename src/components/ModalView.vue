<template>
  <div class="modal">
    <div class="overlay" @click="emit('closeModal')"></div>
    <div class="modal-card">
      <slot />
    </div>
  </div>
</template>

<script>
import { onBeforeUnmount } from 'vue'

const useKeydown = (fn) => {
  const onKeydown = (event) => {
    console.log(event.key)
    if (event.key === 'Escape') {
      fn()
    }
  }

  window.addEventListener('keydown', onKeydown)

  onBeforeUnmount(() => {
    window.removeEventListener('keydown', onKeydown)
  })
}

export default {
  setup(props, { emit }) {
    useKeydown(() => {
      emit('closeModal')
    })

    return {
      emit,
    }
  },
}
</script>

<style lang="scss" scoped></style>
