<template>
  <div>
    <table class="mail-table">
      <tbody>
        <tr
          v-for="email in unarchivedEmails"
          :key="email.id"
          :class="['clickable', email.read ? 'read' : '']"
          @click="openEmail(email)"
        >
          <td>
            <input type="checkbox" />
          </td>
          <td>{{ email.from }}</td>
          <td>
            <p>
              <strong>{{ email.subject }}</strong> - {{ email.body }}
            </p>
          </td>
          <td class="date">
            {{ dateFormat(email) }}
          </td>
          <td>
            <button @click="archivedEmail(email)">Archive</button>
          </td>
        </tr>
      </tbody>
    </table>
    <div v-if="openedEmail">
      <h2>{{ openedEmail.subject }}</h2>
      <p>{{ openedEmail.body }}</p>
    </div>
  </div>
</template>

<script>
import { format } from 'date-fns';
import axios from 'axios';

export default {
  name: 'MailTable',
  // data se cambio por async y para que funcione necesitamos cambiar el data por setup OJO vue3 y setup es muy similiar a data
  // . Si cambiamos el data por setup necesitamos agregar un componente <Suspend> y en el siguente
  // commit quedará demostrado el cambio.

  async setup() {
    let { data: emails } = await axios.get('http:///localhost:4000/emails')

    return {
      format,
      emails,
      openedEmail: null,
    };
  },
  computed: {
    unarchivedEmails() {
      return this.emails.filter((e) => !e.archived);
    },
    // sortedEmails() {
    //   return this.emails
    //     .filter((e) => !e.archived)
    //     .sort((e1, e2) => e1.sentAt < e2.sentAt ? 1 : -1);
    // },
  },
  methods: {
    openEmail(email) {
      email.read = true;
      this.updateEmail(email);
      this.openedEmail = email;
    },
    archivedEmail(email) {
      email.archived = true;
      this.updateEmail(email);
    },
    updateEmail(email) {
      axios.put(`http://localhost:4000/emails/${email.id}`, email)
    },
    dateFormat(email) {
      return format(new Date(email.sentAt), 'yyyy-MM-dd');
    },
  }
};
</script>

<style scoped></style>
