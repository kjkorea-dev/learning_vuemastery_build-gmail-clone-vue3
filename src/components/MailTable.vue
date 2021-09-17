<template>
  <BulkActionBar :emails="unarchivedEmails" />
  <table class="mail-table">
    <tr
      v-for="email in unarchivedEmails"
      :key="email.id"
      :class="['clickable', email.read ? 'read' : '']"
    >
      <td>
        <input
          type="checkbox"
          @click="emailSelection.toggle(email)"
          :checked="emailSelection.emails.has(email)"
        />
      </td>
      <td @click="openEmail(email)">{{ email.from }}</td>
      <td @click="openEmail(email)">
        <p>
          <strong>{{ email.subject }}</strong> - {{ email.body }}
        </p>
      </td>
      <td class="date" @click="openEmail(email)">
        {{ format(new Date(email.sentAt), 'MMM do yyyy') }}
      </td>
      <td><button @click="archiveEmail(email)">Archive</button></td>
    </tr>
  </table>
  <ModalView v-if="openedEmail" @closeModal="openedEmail = null">
    <MailView :email="openedEmail" @changeEmail="changeEmail" />
  </ModalView>
</template>

<script>
import { format } from 'date-fns'
import { ref } from 'vue'
import axios from 'axios'
import useEmailSelection from '@/composables/use-email-selection.js'
import MailView from '@/components/MailView.vue'
import ModalView from '@/components/ModalView.vue'
import BulkActionBar from '@/components/BulkActionBar.vue'

export default {
  async setup() {
    const emails = ref([])
    const response = await axios.get('http://localhost:3000/emails')
    emails.value = response.data
    const openedEmail = ref(null)

    return {
      format,
      emails,
      openedEmail,
      emailSelection: useEmailSelection(),
    }
  },
  components: {
    MailView,
    ModalView,
    BulkActionBar,
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
      this.openedEmail = email
      if (email) {
        email.read = true
        this.updateEmail(email)
      }
    },
    archiveEmail(email) {
      email.archived = true
      this.updateEmail(email)
    },
    changeEmail({ toggleRead, toggleArchive, save, closeModal, changeIndex }) {
      const email = this.openedEmail
      if (toggleRead) {
        email.read = !email.read
      }
      if (toggleArchive) {
        email.archived = !email.archived
      }
      if (save) {
        this.updateEmail(email)
      }
      if (closeModal) {
        this.openedEmail = null
      }
      if (changeIndex) {
        const emails = this.unarchivedEmails
        const currentIndex = emails.indexOf(this.openedEmail)
        const newEmail = emails[currentIndex + changeIndex]
        this.openEmail(newEmail)
      }
    },
    updateEmail(email) {
      axios.put(`http://localhost:3000/emails/${email.id}`, email)
    },
  },
}
</script>
<style scoped></style>
