<template>
  <table class="mail-table">
    <tr
      v-for="email in unarchivedEmails"
      :key="email.id"
      :class="['clickable', email.read ? 'read' : '']"
    >
      <td><input type="checkbox" /></td>
      <td @click="openEmail(email)">
        <p>
          <strong>{{ email.subject }}</strong> - {{ email.body }}
        </p>
      </td>
      <td class="date">
        {{ format(new Date(email.sentAt), 'MMM do yyyy') }}
      </td>
      <td><button @click="archiveEmail(email)">Archive</button></td>
    </tr>
  </table>
  <MailView v-if="openedEmail" :email="openedEmail" />
</template>

<script>
import { format } from 'date-fns'
import { ref } from 'vue'
import axios from 'axios'
import MailView from '@/components/MailView.vue'

export default {
  async setup() {
    const emails = ref([])
    const openedEmail = ref(null)
    const response = await axios.get('http://localhost:3000/emails')
    emails.value = response.data
    return {
      format,
      emails,
      openedEmail,
    }
  },
  components: {
    MailView,
  },
  computed: {
    sortedEmails() {
      return [...this.emails].sort((e1, e2) => {
        return e1.sentAt < e2.sentAt ? 1 : -1
      })
    },
    unarchivedEmails() {
      return this.sortedEmails.filter((e) => !e.archived)
    },
  },
  methods: {
    openEmail(email) {
      email.read = true
      this.updateEmail(email)
      this.openedEmail = email
    },
    archiveEmail(email) {
      email.archived = true
      this.updateEmail(email)
    },
    updateEmail(email) {
      axios.put(`http://localhost:3000/emails/${email.id}`, email)
    },
  },
}
</script>
<style scoped></style>
