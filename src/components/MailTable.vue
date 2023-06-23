<template>
  <table class="mail-table">
    <tbody>
      <tr
        v-for="email in sortedEmails"
        :key="email.id"
        :class="['clickable', email.read ? 'read' : '']"
        @click="email.read = true"
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
          {{ format(new Date(email.sentAt), 'yyyy-MM-dd') }}
        </td>
        <td>
          <button @click="email.archived = true">Archive</button>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>
import { format } from 'date-fns';
import { ref } from 'vue';
import axios from 'axios';

export default {
  name: 'MailTable',
  // data se cambio por async y para que funcione necesitamos cambiar el data por setup OJO vue3 y setup es muy similiar a data
  // . Si cambiamos el data por setup necesitamos agregar un componente <Suspend> y en el siguente
  // commit quedará demostrado el cambio.


  async setup() {
    let emails = await axios.get('http://my-json-server.typicode.com/alexismansilla/vue-mastering-1/emails')
    console.log(emails)

    return {
      format,
      emails = emails
    };
  },
  computed: {
    sortedEmails() {
      return this.emails
        .filter((e) => !e.archived)
        .sort((e1, e2) => e2.sentAt - e1.sentAt);
    },
  },
};
</script>

<style scoped></style>
