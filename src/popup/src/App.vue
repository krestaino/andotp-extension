<template>
  <main>
    <header>
      <h1 v-if="!searchActive">andOTP</h1>
      <div class="actions" :style="{ flex: searchActive ? 1 : 0 }">
        <Search :searchActive="searchActive" @toggleSearchActive="searchActive = !searchActive" @onSearchQueryChange="query => (searchQuery = query)" />
        <Options />
      </div>
      <div ref="timeRemaining" class="time-remaining" :style="{ animationDuration, width }" v-on:animationend="handleAnimationEnd()" />
    </header>
    <section>
      <ul v-if="accounts.length">
        <li v-for="account in accountFilter || accounts" :key="account.label">
          <Icon :letter="account.label.charAt(0)" />
          <div class="text">
            <div class="secret">{{ getCode(account.secret) }}</div>
            <div class="label">{{ account.label }}</div>
          </div>
          <Copy :code="getCode(account.secret)" />
        </li>
      </ul>
      <div class="no-accounts" v-else>
        Open the
        <a href="#" @click.prevent="handleOptionsClick">options</a> to get started.
      </div>
    </section>
  </main>
</template>

<script>
import 'reset-css';
import Search from './Search.vue';
import Options from './Options.vue';
import Icon from './Icon.vue';
import Copy from './Copy.vue';

export default {
  components: {
    Search,
    Options,
    Icon,
    Copy,
  },
  computed: {
    accountFilter() {
      if (this.searchActive) {
        return this.accounts.filter(account => account.label.toLowerCase().includes(this.searchQuery.toLowerCase()));
      }
    },
  },
  data() {
    return {
      accounts: [],
      animationDuration: undefined,
      searchActive: false,
      searchQuery: '',
      width: undefined,
    };
  },
  mounted() {
    this.getAccounts();
    this.setAnimtion();
  },
  watch: {
    accounts() {
      if (this.accounts.length > 8) {
        document.querySelector('html').style.height = '600px';
      }
    },
  },
  methods: {
    handleOptionsClick() {
      chrome.runtime.openOptionsPage();
    },
    handleAnimationEnd() {
      const { timeRemaining } = this.$refs;

      timeRemaining.style.animation = 'none';
      timeRemaining.offsetHeight;
      timeRemaining.style.animation = null;
      this.setAnimtion();
    },
    getAccounts() {
      chrome.storage.local.get(['accounts'], result => {
        this.accounts = result.accounts.sort((a, b) => a.label.localeCompare(b.label));
      });
    },
    getCode(secret) {
      return window.otplib.authenticator.generate(secret);
    },
    getTimeRemaining() {
      return window.otplib.authenticator.timeRemaining();
    },
    getWidth() {
      return (this.getTimeRemaining() / 30) * 100 + '%';
    },
    getAnimationDuration() {
      return this.getTimeRemaining() + 's';
    },
    setAnimtion() {
      this.width = this.getWidth();
      this.animationDuration = this.getAnimationDuration();
    },
  },
};
</script>

<style lang="scss">
html {
  background-color: #f1f1f1;
  box-sizing: border-box;
  overflow-y: scroll;
  width: 270px;
  --primary: #388e3c;
  --accent: #fdca00;
}

*,
*:before,
*:after {
  box-sizing: inherit;
}

::selection {
  background-color: var(--primary);
  color: #fff;
}

main {
  color: #444;
  display: flex;
  flex-direction: column;
  font-family: 'Roboto', sans-serif;
  line-height: 1.35;
  width: 100%;

  section {
    align-items: center;
    display: flex;
    flex-direction: column;
    justify-content: center;
    flex: 1;
    padding: 0.5rem;
  }

  header {
    align-items: center;
    background-color: var(--primary);
    color: #fff;
    display: flex;
    justify-content: space-between;
    padding: 0.5rem 0.5rem 0.5rem 1rem;
    position: relative;

    button {
      &:hover {
        color: var(--accent);
      }
    }

    .time-remaining {
      animation-iteration-count: infinite;
      animation: linear 0s 1 shrink;
      background-color: var(--accent);
      bottom: 0;
      height: 3px;
      left: 0;
      position: absolute;
      width: 100%;

      @keyframes shrink {
        0% {
          width: 'inherit';
        }
        100% {
          width: 0;
        }
      }
    }
  }

  ul {
    width: 100%;

    li {
      background-color: #fff;
      border-radius: 4px;
      box-shadow: rgba(60, 64, 67, 0.3) 0px 1px 2px 0px, rgba(60, 64, 67, 0.15) 0px 1px 3px 1px;
      display: flex;
      padding: 0.5rem;

      & + li {
        margin-top: 0.5rem;
      }

      .text {
        flex-grow: 1;
        margin-left: 0.5rem;
      }

      .secret {
        font-size: 1rem;
        font-weight: bold;
      }

      .label {
        color: #666;
        font-size: 0.8rem;
        overflow: hidden;
        white-space: nowrap;
        max-width: 145px;
        text-overflow: ellipsis;
      }
    }
  }

  .no-accounts {
    padding: 1rem 0;
  }

  a {
    color: var(--primary);
    font-weight: bold;
  }

  button {
    align-items: center;
    background-color: transparent;
    border: 0;
    color: inherit;
    cursor: pointer;
    display: flex;
    justify-content: center;
    outline: 0;
    padding: 0;
    transition: 0.3s;
  }

  svg {
    fill: currentColor;
  }

  .actions {
    display: flex;
    justify-content: space-between;

    button {
      padding: 0.25rem;
    }

    button + button {
      margin-left: 0.5rem;
    }
  }
}
</style>
