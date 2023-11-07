<template>
  <div class="not-selected">
    <img src="../not-selected-img.svg" />
    <div class="not-selected-header">
      <h2 class="title">Select an Element</h2>
      <p class="subtitle">Before editing, select an element on the canvas.</p>
    </div>
    <p>Make sure that the element has one class. <strong>Combo class styles are not supported at the moment.</strong></p>
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

    img {
      width: 112px;
      height: auto;
    }

    .not-selected-header{
      margin: 16px 0 32px;
    }

    .title {
      font-size: 24px;
      line-height: 1.1;
      margin-bottom: 8px;
    }

    .subtitle {
      font-size: 16px;
    }

    p {
      font-size: var(--font-size-small);
    }
  }
</style>