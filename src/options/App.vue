<template>
  <main>
    <header>
      <h1>{{ accounts.length }} active accounts</h1>
    </header>
    <label>Restore (plain-text)</label>
    <input type="file" @change="loadImport">
    <button @click="restore">Import</button>
  </main>
</template>

<script>
import 'reset-css';
const aes4js = require('aes4js');

export default {
  name: 'App',
  data: () => ({
    accounts: [],
    import: undefined,
  }),
  mounted() {
    chrome.storage.local.get(['accounts'], result => {
      this.accounts = result.accounts;
    });
  },
  methods: {
    loadImport(event) {
      const file = event.target.files[0];
      const reader = new FileReader();

      reader.onload = e => (this.import = JSON.parse(e.target.result));
      reader.readAsText(file);
    },
    restore() {
      chrome.storage.local.set({ accounts: this.import }, () => {
        this.accounts = this.import;
      });
    },
  },
};
</script>

<style lang="scss" scoped>
main {
  font-family: 'Roboto', sans-serif;
  padding: 0 1rem 1rem 1rem;
}
</style>
