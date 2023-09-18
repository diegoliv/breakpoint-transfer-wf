<template>
  <header>
    <div class="header-title">
      <h1>Beautiful Shadows</h1>
    </div>
    <p>Effortlessly create realistic shadows for your elements.</p>
  </header>
</template>

<style lang="scss">
header {
  padding: 8px;
  color: var(--text1);

  a {
    color: var(--actionPrimaryText);

    &:hover {
      color: var(--actionPrimaryTextHover);
    }
  }

  .header-title {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 8px;

    a {
      display: block;
      width: 24px;
      height: 24px;
      text-decoration: none;
      color: var(--actionPrimaryText);

      &:hover {
        color: var(--actionPrimaryTextHover);
      }
    }
  }
}
</style>
