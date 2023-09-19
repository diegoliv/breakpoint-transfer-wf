<template>
  <div class="search-description">
    <h2>Select an Element</h2>
    <p>Before editing, select an element on the canvas. <strong>Make sure that the element has at least one class</strong> - the shadow styles will be applied to the last class of the element.</p>
  </div>
</template>

<style lang="scss">
  .search-description {
    width: 100%;
    min-height: 240px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 24px;
    text-align: center;
    color: var(--text1);
    font-size: var(--font-size-large);
  }
</style>