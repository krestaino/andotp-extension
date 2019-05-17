<template>
  <div>
    <span v-text="letter" />
  </div>
</template>

<script>
export default {
  props: ['letter'],
};
</script>

<style lang="scss" scoped>
div {
  span {
    align-items: center;
    background-color: #aaa;
    border-radius: 2px;
    color: #fff;
    display: flex;
    font-size: 1.25rem;
    font-weight: bold;
    height: 38px;
    justify-content: center;
    text-transform: uppercase;
    width: 38px;
  }
}
</style>
