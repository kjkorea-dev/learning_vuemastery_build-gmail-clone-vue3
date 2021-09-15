<template>
  <div class="email-display">
    <div class="toolbar">
      <button @click="toggleArchive">
        {{ email.archived ? 'Move to Inbox' : 'Archive' }} (e)
      </button>
      <button @click="toggleRead">
        Mark {{ email.read ? 'Unread' : 'Read' }} (r)
      </button>
      <button @click="goNewer">Newer (k)</button>
      <button @click="goOlder">Older (j)</button>
    </div>
    <h2 class="mb-0">
      Subject: <strong>{{ email.subject }}</strong>
    </h2>
    <div>
      <em
        >From {{ email.from }} on
        {{ format(new Date(email.sentAt), 'MMM do yyyy') }}</em
      >
    </div>
    <div v-html="marked(email.body)" />
  </div>
</template>

<script>
import { format } from 'date-fns'
import marked from 'marked'
import useKeydown from '@/composables/use-keydown'
export default {
  setup(props, { emit }) {
    const toggleRead = () => {
      emit('changeEmail', { toggleRead: true, save: true })
    }
    const toggleArchive = () => {
      emit('changeEmail', { toggleArchive: true, save: true, closeModal: true })
    }
    const goNewer = () => {
      emit('changeEmail', { changeIndex: -1 })
    }
    const goOlder = () => {
      emit('changeEmail', { changeIndex: 1 })
    }

    useKeydown([
      {
        key: 'r',
        fn: toggleRead,
      },
      {
        key: 'e',
        fn: toggleArchive,
      },
      {
        key: 'k',
        fn: goNewer,
      },
      {
        key: 'j',
        fn: goOlder,
      },
    ])

    return {
      format,
      marked,
      toggleRead,
      toggleArchive,
      goNewer,
      goOlder,
    }
  },
  props: {
    email: {
      type: Object,
      required: true,
    },
  },
}
</script>

<style scoped></style>
