<template>
  <div class="email-display">
    <div class="toolbar">
      <button @click="toggleArchive">
        {{ email.archived ? 'Move to Inbox' : 'Archive' }} (e)
      </button>
      <button @click="toggleRead">
        Mark {{ email.read ? 'Unread' : 'Read' }} (r)
      </button>
      <button>Newer</button>
      <button>Older</button>
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
import { toRefs } from 'vue'
import axios from 'axios'
import { format } from 'date-fns'
import marked from 'marked'
import useKeydown from '@/composables/use-keydown'
export default {
  setup(props) {
    const { email } = toRefs(props)
    const toggleRead = () => {
      email.value.read = !email.value.read
      axios.put(`http://localhost:3000/emails/${email.value.id}`, email.value)
    }

    const toggleArchive = () => {
      email.value.archived = !email.value.archived
      axios.put(`http://localhost:3000/emails/${email.value.id}`, email.value)
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
    ])

    return {
      format,
      marked,
      toggleRead,
      toggleArchive,
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
