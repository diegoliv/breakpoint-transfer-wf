<template>
  <header
    :style="bgStyle"
  >
    <div class="box" :style="boxStyle"></div>
  </header>
</template>

<script>
export default {
  props: ['bgColor', 'boxColor', 'shadow'],
  computed: {
    bgStyle() {
      return `background-color: ${this.bgColor}`
    },
    boxStyle() {
      return `background-color: ${this.boxColor}; box-shadow: ${this.shadow};`
    }
  }
}
</script>

<style lang="scss">
header {
  width: 100%;
  height: 240px;
  padding: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  overflow: hidden;

  .box {
    width: 124px;
    height: 124px;
    border-radius: 8px;
  }
}
</style>
