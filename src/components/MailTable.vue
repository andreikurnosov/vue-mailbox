<template>
<bulk-action-bar :emails="unarchivedEmails"  />
  <table class="mail-table">
    <tbody>
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
        <td class="date">{{ format(new Date(email.sentAt), 'MMM do yyyy') }}</td>
        <td><button @click="archiveEmail(email)" class="btn--archive">&#8659;</button></td>
      </tr>
    </tbody>
  </table>

  <modal-view v-if="openedEmail" @closeModal="openedEmail = null">
    <mail-view :email="openedEmail" @changeEmail="changeEmail" />
  </modal-view>
</template>

<script>
import { format } from 'date-fns'
import axios from 'axios'
import { computed, reactive, ref } from 'vue'
import MailView from './MailView.vue'
import ModalView from './ModalView.vue'
import useEmailSelection from '../composables/use-email-selection'
import BulkActionBar from '@/components/BulkActionBar.vue'

export default {
  components: {
    MailView,
    ModalView,
    BulkActionBar,
    BulkActionBar
  },

  async setup() {
    let { data } = await axios.get('http://localhost:3000/emails')
    let emails = reactive(data)
    let openedEmail = ref(null)

    const sortedEmails = computed(() =>
      emails.sort((e1, e2) => {
        return e1.sentAt < e2.sentAt ? 1 : -1
      })
    )

    const unarchivedEmails = computed(() => sortedEmails.value.filter(e => !e.archived))

    function openEmail(email) {
      openedEmail.value = email

      if (email) {
        email.read = true
        updateEmail(email)
      }
    }

    function archiveEmail(email) {
      email.archived = true
      updateEmail(email)
    }

    function updateEmail(email) {
      axios.put(`http://localhost:3000/emails/${email.id}`, email)
    }

    function changeEmail({ toggleRead, toggleArchive, save, closeModal, changeIndex }) {
      let email = openedEmail.value

      if (toggleRead) {
        email.read = !email.read
      }

      if (toggleArchive) {
        email.archived = !email.archived
      }

      if (save) {
        updateEmail(email)
      }

      if (closeModal) {
        openedEmail.value = null
      }

      if (changeIndex) {
        let emails = unarchivedEmails.value
        let currentIndex = emails.indexOf(openedEmail.value)
        let newEmail = emails[currentIndex + changeIndex]

        openEmail(newEmail)
      }
    }

    return {
      emailSelection: useEmailSelection(),
      openEmail,
      archiveEmail,
      changeEmail,
      format,
      emails,
      openedEmail,
      unarchivedEmails,
      sortedEmails
    }
  }
}
</script>
<style>
.btn--archive {
  border: 0;
  color: tomato;
  font-size: 25px;
  background: lightgrey;
  padding: 5px;
}
</style>
