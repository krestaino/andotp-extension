<template>
  <div>
    <button @click="handleToggleSearchActive()" title="Search">
      <svg v-if="searchActive" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24">
        <path d="M0 0h24v24H0z" fill="none"></path>
        <path d="M20 11H7.83l5.59-5.59L12 4l-8 8 8 8 1.41-1.41L7.83 13H20v-2z"></path>
      </svg>
      <svg v-else xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24">
        <path
          d="M15.5 14h-.79l-.28-.27C15.41 12.59 16 11.11 16 9.5 16 5.91 13.09 3 9.5 3S3 5.91 3 9.5 5.91 16 9.5 16c1.61 0 3.09-.59 4.23-1.57l.27.28v.79l5 4.99L20.49 19l-4.99-5zm-6 0C7.01 14 5 11.99 5 9.5S7.01 5 9.5 5 14 7.01 14 9.5 11.99 14 9.5 14z"
        ></path>
        <path d="M0 0h24v24H0z" fill="none"></path>
      </svg>
    </button>
    <input v-if="searchActive" v-model="searchQuery" placeholder="Search..." ref="searchQueryInput" type="text" />
  </div>
</template>

<script>
export default {
  props: ['searchActive'],
  data: () => ({
    searchQuery: '',
  }),
  methods: {
    handleToggleSearchActive() {
      this.$emit('toggleSearchActive');
    },
  },
  updated() {
    if (this.searchActive) {
      this.$refs.searchQueryInput.focus();
    }
  },
  watch: {
    searchQuery() {
      this.$emit('onSearchQueryChange', this.searchQuery);
    },
  },
};
</script>

<style lang="scss" scoped>
div {
  display: flex;

  input[type='text'] {
    background-color: transparent;
    border: 0;
    color: #fff;
    font-size: 0.9rem;
    margin-left: 0.5rem;
    outline: 0;
    width: 160px;

    &::placeholder {
      color: rgba(#fff, 0.5);
      font-size: 0.8rem;
    }
  }
}
</style>
