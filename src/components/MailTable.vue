<template>
  <table class="mail-table">
    <tbody>
      <tr
        v-for="email in unarchivedEmails"
        :key="email.id"
        :class="['clickable', email.read ? 'read' : '']"
        @click="openEmail(email)"
      >
        <td>{{ email.from }}</td>
        <td>
          <input type="checkbox" />
        </td>
        <td>
          <p>
            <strong>{{ email.subject }}</strong> - {{ email.body }}
          </p>
        </td>
        <td class="date">{{ format(new Date(email.sentAt), 'MMM do yyyy') }}</td>
        <td><button @click="archiveEmail(email)" class="btn--archive">&#8659;</button></td>
      </tr>
    </tbody>
  </table>

  <mail-view v-if="openedEmail" :email="openedEmail" />


</template>

<script>
import { format } from 'date-fns'
import axios from 'axios'
import { ref } from 'vue'
import MailView from './MailView.vue'

export default {
  components: {
    MailView
  },
  async setup() {
    let { data } = await axios.get('http://localhost:3000/emails')
    let emails = data
    let openedEmail = ref(null)

    function openEmail(email) {
      email.read = true
      openedEmail.value = email
      updateEmail(email)
    }

    function archiveEmail(email) {
      email.archived = true
      updateEmail(email)
    }

    function updateEmail(email) {
      axios.put(`http://localhost:3000/emails/${email.id}`, email)
    }

    return {
      openEmail,
      archiveEmail,
      format,
      emails,
      openedEmail
    }
  },
  computed: {
    sortedEmails() {
      return this.emails.sort((e1, e2) => {
        return e1.sentAt < e2.sentAt ? 1 : -1
      })
    },
    unarchivedEmails() {
      return this.sortedEmails.filter(e => !e.archived)
    }
  }
}
</script>
<style scoped>
.btn--archive {
  border: 0;
  color: tomato;
  font-size: 25px;
  background: lightgrey;
  padding: 5px;
}
</style>
