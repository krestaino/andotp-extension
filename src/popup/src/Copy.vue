<template>
  <button @click="copy(code)" title="Copy to clipboard">
    <svg
      v-if="icon === 'copy'"
      xmlns="http://www.w3.org/2000/svg"
      width="24"
      height="24"
      viewBox="0 0 24 24"
    >
      <path fill="none" d="M0 0h24v24H0z"></path>
      <path
        d="M16 1H4c-1.1 0-2 .9-2 2v14h2V3h12V1zm-1 4l6 6v10c0 1.1-.9 2-2 2H7.99C6.89 23 6 22.1 6 21l.01-14c0-1.1.89-2 1.99-2h7zm-1 7h5.5L14 6.5V12z"
      ></path>
    </svg>
    <svg
      v-if="icon === 'success'"
      xmlns="http://www.w3.org/2000/svg"
      width="24"
      height="24"
      viewBox="0 0 24 24"
    >
      <path d="M0 0h24v24H0z" fill="none"></path>
      <path
        d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-2 15l-5-5 1.41-1.41L10 14.17l7.59-7.59L19 8l-9 9z"
      ></path>
    </svg>
    <svg
      v-if="icon === 'error'"
      xmlns="http://www.w3.org/2000/svg"
      width="24"
      height="24"
      viewBox="0 0 24 24"
    >
      <path d="M0 0h24v24H0z" fill="none"></path>
      <path
        d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm1 15h-2v-2h2v2zm0-4h-2V7h2v6z"
      ></path>
    </svg>
  </button>
</template>

<script>
export default {
  props: ['code'],

  data: () => ({
    icon: 'copy',
  }),

  methods: {
    copy(code) {
      navigator.clipboard
        .writeText(code)
        .then(() => {
          this.icon = 'success';
          setTimeout(() => {
            this.icon = 'copy';
          }, 2000);
        })
        .catch(err => {
          this.icon = 'error';
        });
    },
  },
};
</script>

<style lang="scss" scoped>
button {
  color: #777;
  margin-left: 1rem;

  &:hover {
    color: #69be68;
  }
}

svg {
  fill: currentColor;
  height: 1.25rem;
  width: 1.25rem;
}
</style>