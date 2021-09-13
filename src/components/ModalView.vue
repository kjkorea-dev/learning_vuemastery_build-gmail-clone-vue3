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

const useKeydown = (keyCombos) => {
  const onKeydown = (event) => {
    console.log(event.key)
    const kc = keyCombos.find((kc) => kc.key == event.key)
    if (kc) {
      kc.fn()
    }
  }

  window.addEventListener('keydown', onKeydown)

  onBeforeUnmount(() => {
    window.removeEventListener('keydown', onKeydown)
  })
}

export default {
  setup(props, { emit }) {
    useKeydown([
      {
        key: 'Escape',
        fn: () => {
          emit('closeModal')
        },
      },
      {
        key: 'Enter',
        fn: () => {
          console.log('A different funtion')
        },
      },
    ])

    return {
      emit,
    }
  },
}
</script>

<style lang="scss" scoped></style>
