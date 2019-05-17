<template>
  <main>
    <header>
      <h1>andOTP</h1>
      <div class="actions">
        <Refresh :onRefresh="handleRefreshClick" />
        <Options />
      </div>
      <div ref="timeRemaining" class="time-remaining" :style="{ animationDuration, width }" v-on:animationend="handleAnimationEnd()" />
    </header>
    <section>
      <ul v-if="accounts.length">
        <li v-for="account in accounts" :key="account.label">
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
import Refresh from './Refresh.vue';
import Options from './Options.vue';
import Icon from './Icon.vue';
import Copy from './Copy.vue';

export default {
  components: {
    Refresh,
    Options,
    Icon,
    Copy,
  },
  data() {
    return {
      accounts: [],
      animationActive: true,
      animationDuration: undefined,
      width: undefined,
    };
  },
  mounted() {
    this.getAccounts();
    this.setAnimtion();
  },
  methods: {
    handleRefreshClick() {
      this.accounts = [];
      this.getAccounts();
    },
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
::selection {
  background-color: #69be68;
  color: #fff;
}

main {
  background-color: #f1f1f1;
  color: #444;
  display: flex;
  flex-direction: column;
  font-family: 'Roboto', sans-serif;
  line-height: 1.35;
  min-width: 270px;

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
    background-color: #69be68;
    color: #fff;
    display: flex;
    justify-content: space-between;
    padding: 0.5rem 0.5rem 0.5rem 1rem;
    position: relative;

    .time-remaining {
      animation-iteration-count: infinite;
      animation: linear 0s 1 shrink;
      background-color: #fdca00;
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
    color: #69be68;
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
  }

  svg {
    fill: currentColor;
  }

  .actions {
    display: flex;

    button + button {
      margin-left: 0.5rem;
    }
  }
}
</style>
