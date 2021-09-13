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

const useKeydown = (keyPressed, fn) => {
  const onKeydown = (event) => {
    console.log(event.key)
    if (event.key === keyPressed) {
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
    useKeydown('Escape', () => {
      emit('closeModal')
    })
    useKeydown('Enter', () => {
      console.log('A different funtion')
    })

    return {
      emit,
    }
  },
}
</script>

<style lang="scss" scoped></style>
