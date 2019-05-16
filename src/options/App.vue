<template>
  <main>
    <header>
      <div
        class="tip"
      >Restore accounts using a backup from the app. Limited to plain-text JSON files right now.</div>
      <h1 v-if="accounts.length">
        <span>{{ accounts.length }}</span>
        <span>&nbsp;account</span>
        <span v-if="accounts.length > 1">s</span>
      </h1>
    </header>
    <section>
      <ul>
        <li v-for="account in accounts" :key="account.label">
          <span>{{ account.label }}</span>
        </li>
      </ul>
    </section>
    <div class="actions">
      <Button class="delete" @click="deleteAccounts">Delete</Button>
      <Button class="import">
        <span>Restore</span>
        <input type="file" ref="fileInput" @change="loadImport">
      </Button>
    </div>
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

      if (file.type === 'application/json') {
        reader.onload = e => {
          const accounts = JSON.parse(e.target.result);
          this.restoreAccounts(accounts);
        };
        reader.readAsText(file);
      } else {
        reader.onload = e => console.log(e.target.result);
        reader.readAsArrayBuffer(file);
      }
    },
    restoreAccounts(accounts) {
      chrome.storage.local.set({ accounts: accounts }, () => {
        this.accounts = accounts;
      });
    },
    deleteAccounts() {
      chrome.storage.local.set({ accounts: [] }, () => {
        this.$refs.fileInput.value = '';
        this.accounts = [];
      });
    },
  },
};
</script>

<style lang="scss" scoped>
main {
  background-color: #f1f1f1;
  color: #444;
  font-family: 'Roboto', sans-serif;
  line-height: 1.35;
  padding: 1rem;
  min-width: 300px;

  header {
    h1 {
      display: flex;
      font-weight: bold;
      margin: 1rem 0 0.5rem 0;
    }
  }

  section {
    li {
      list-style: square;
      margin-left: 1.5rem;

      & + li {
        margin-top: 0.5rem;
      }
    }
  }

  .import {
    position: relative;
    background: #69be68;
    border-color: #69be68 !important;
    color: #fff;

    &:hover {
      background: #73d671;
      color: #fff;
    }

    span {
      pointer-events: none;
    }

    input {
      cursor: pointer;
      height: 100%;
      left: 0;
      opacity: 0;
      position: absolute;
      top: 0;
      width: 100%;
    }
  }

  .actions {
    display: flex;
    justify-content: flex-end;
    margin-top: 1rem;
  }

  button {
    align-items: center;
    background: #fff;
    border: 1px solid #fff;
    border-radius: 4px;
    box-shadow: none !important;
    color: inherit;
    cursor: pointer;
    display: flex;
    justify-content: center;
    outline: 0;
    padding: 0.5rem 1rem;
    text-shadow: none !important;
    transition: 0.3s;

    &:hover {
      background: inherit;
      color: inherit;
    }

    &.delete {
      border: 1px solid #bbb;
      color: #777;

      &:hover {
        background: #fff;
        border: 1px solid #e26767;
        color: #e26767;
      }
    }

    & + button {
      margin-left: 0.5rem;
    }
  }
}
</style>
