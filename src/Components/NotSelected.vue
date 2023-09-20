<template>
  <div class="not-selected">
    <img src="../not-selected-img.svg" />
    <div class="not-selected-header">
      <h2 class="title">Select an Element</h2>
      <p class="subtitle">Before editing, select an element on the canvas.</p>
    </div>
    <p><strong>Make sure that the element has at least one class.</strong> The shadow styles will be applied to the last class of the element.</p>
  </div>
</template>

<style lang="scss">
  .not-selected {
    width: 100%;
    min-height: 240px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 24px;
    text-align: center;
    color: var(--text1);

    .not-selected-header{
      margin: 16px 0 32px;
    }

    .title {
      font-size: 24px;
    }

    .subtitle {
      font-size: 16px;
    }

    p {
      font-size: var(--font-size-small);
    }
  }
</style>