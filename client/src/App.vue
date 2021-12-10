<template>
  <div id="app" class="container d-flex flex-column justify-content-center align-items-center mt-5">
    <div v-if="showAlert" class="alert alert-danger" role="alert">
      Update vorhanden! Bitte App neu starten!
    </div>
    <h3>Hier könnte Ihre Werbung sein!</h3>
    <img src="/employees.jpg" width="50%" alt="menschen" /><br />
    <div v-if="offline" class="alert alert-danger" role="alert">
      You are offline!
    </div>
    <ButtonGet @get="fetchData"></ButtonGet>
    <CardView :employees="employees" @del="delEmployee"></CardView>
  </div>
</template>

<script>
import ButtonGet from '@/components/ButtonGet.vue';
import CardView from '@/components/CardView.vue';
import axios from 'axios';
export default {
  name: 'app',
  components: {
    ButtonGet,
    CardView,
  },
  data() {
    return {
      employees: [],
      serverAddress: process.env.VUE_APP_SERVER,
      showAlert: false,
      offline: false,
    };
  },
  methods: {
    async fetchData() {
      const emp = await axios({ method: 'GET', url: `${process.env.VUE_APP_SERVER}/employees` });
      this.employees = emp.data;
      console.log('fetchData called');
    },
    async delEmployee(e) {
      await axios({ method: 'DELETE', url: `${process.env.VUE_APP_SERVER}/employees/${e.id}` });
      this.fetchData();
      console.log('delEmployee called');
    },
    updateAvailable() {
      this.showAlert = true;
      if (confirm(`New content is available!. Click OK to refresh!`)) {
        window.location.reload();
      }
    },
  },
  created() {
    document.addEventListener('swUpdated', this.updateAvailable, { once: true });
    this.offline=!navigator.online
    window.addEventListener('online', () => (this.offline = false));
    window.addEventListener('offline', () => (this.offline = true));
  },
};
</script>

<style></style>
