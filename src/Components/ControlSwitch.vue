<template>
  <div class="control-group switch">
    <input 
      class="control-switch-checkbox"
      type="checkbox" 
      :name="id" 
      :id="id"
      :value="modelValue"
      @change="$emit('update:modelValue', $event.target.value)"
      @focus="isFocused = true"
      @blur="isFocused = false"
    />
    <label :for="id">
      <span class="control-label">{{ label }}</span>
      <div 
        class="control-switch"
        :class="isFocused ? 'active' : ''"        
      >
        <div class="control-switch-inner"></div>
      </div>
    </label>
    <div class="control-helper">
      {{ description }}
    </div>
  </div>
</template>

<script>

export default {
  props: ['modelValue', 'label', 'id', 'description'],
  emits: ['update:modelValue'],
  data() {
    return {
      isFocused: false
    }
  },
}
</script>

<style src="@vueform/slider/themes/default.css"></style>
<style lang="scss">
.control-group.switch {
  label {
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
    color: var(--text1);
    font-weight: var(--font-weight-medium);
    font-size: var(--font-size-big);
  }

  .control-switch-checkbox {
    display: none;

    + label .control-switch {
      position: relative;
      display: flex;
      justify-content: flex-start;
      width: 40px;
      height: 24px;
      background-color: var(--background4);
      border-radius: 24px;
      box-shadow: insed 0 0 0 1px solid var(--border3);
      cursor: pointer;

      .control-switch-inner {
        width: 24px;
        height: 24px;
        border-radius: 24px;
        background-color: var(--text2);
        border: 1px solid var(--border-3);
      }
    }
  }

  &:checked {
    + label .control-switch {
      justify-content: flex-end;
      background-color: var(--blueIcon);
    }
  }

  .control-helper {
    margin-top: 4px;
    color: var(--text2);
    font-size: var(--font-size-small);
  }
}
</style>