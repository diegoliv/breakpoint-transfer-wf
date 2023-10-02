<template>
  <div class="control-group switch">
    <input 
      class="control-switch-checkbox"
      type="checkbox" 
      :name="id" 
      :id="id"
      :v-model="model"
      @focus="isFocused = true"
      @blur="isFocused = false"
    />
    <label :for="id">
      <span class="control-label">{{ label }}</span>
      <div 
        class="control-switch"
        :class="isFocused ? 'active' : ''"        
      >
        <div class="control-switch-inner">
          <div class="control-switch-handle"></div>
        </div>
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
  computed: {
    model:{
      get() {
        return this.modelValue;
      },
      set(value) {
        this.$emit("update:modelValue", value);
      }
    }    
  }
}
</script>

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
    cursor: pointer;
  }

  .control-switch-checkbox {
    display: none;

    + label .control-switch {
      position: relative;
      width: 40px;
      height: 24px;
      overflow: hidden;
      background-color: var(--background4);
      border-radius: 24px;
      border: 1px solid var(--border3);

      .control-switch-inner {
        position: absolute;
        top: 0;
        left: 0;
        width: 40px;
        height: 22px;
        border-radius: 24px;
        background-color: var(--blueIcon);
        transform: translate3d(-18px, 0, 0);
        transition: transform .15s ease-in-out;
      }

      .control-switch-handle {
        position: absolute;
        top: -1px;
        right: -1px;
        width: 24px;
        height: 24px;
        border-radius: 24px;
        background-color: var(--text2);
        border: 1px solid var(--border3);
      }
    }

    &:checked {
      + label .control-switch-inner {
        transform: translate3d(0, 0, 0);
      }
    }
  }


  .control-helper {
    margin-top: 4px;
    color: var(--text2);
    font-size: var(--font-size-small);
  }
}
</style>