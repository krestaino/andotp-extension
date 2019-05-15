<template>
  <div>
    <span v-text="letter"/>
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
    color: #fff;
    display: flex;
    font-size: 1.25rem;
    font-weight: bold;
    height: 2.15rem;
    justify-content: center;
    text-transform: uppercase;
    width: 2.15rem;
  }
}
</style>
