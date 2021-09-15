<template>
  <div class="email-display">
    <div class="toolbar">
      <button>Archive</button>
      <button @click="toggleRead">
        Mark {{ email.read ? 'Unread' : 'Read' }}
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
export default {
  setup(props) {
    const { email } = toRefs(props)
    const toggleRead = () => {
      email.value.read = !email.value.read
      axios.put(`http://localhost:3000/emails/${email.value.id}`, email.value)
    }
    return {
      format,
      marked,
      toggleRead,
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
