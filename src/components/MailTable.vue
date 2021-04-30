<template>
  <table class="mail-table">
    <tbody>
      <tr
        v-for="email in unarchivedEmails"
        :key="email.id"
        :class="['clickable', email.read ? 'read' : '']"
        @click="readEmail(email)"
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
</template>

<script>
import { format } from 'date-fns'
import axios from 'axios'

export default {
  async setup() {
    let { data } = await axios.get('http://localhost:3000/emails')
    let emails = data

    function readEmail(email) {
      email.read = true
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
      readEmail,
      archiveEmail,
      format,
      emails
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
