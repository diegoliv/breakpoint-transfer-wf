<template>
  <div class="control-group">
    <label :for="id">{{ label }}</label>
    <div class="control">
      <div class="control-slider">
        <Slider 
          :tooltips="false"
          :lazy="false"
          :min="modelMin"
          :max="modelMax"
          :value="modelValue"
          :step="modelMax <= 1 ? -1 : 1"
          @update="$emit('update:modelValue', $event)"
        />
      </div>
      <div 
        class="control-input-wrapper"
        :class="isFocused ? 'active' : ''"
      >
        <input 
          class="control-input"
          type="number" 
          :name="id" 
          :id="id"
          :min="modelMin"
          :max="modelMax"
          :value="modelValue"
          @input="$emit('update:modelValue', $event.target.value)"
          @focus="isFocused = true"
          @blur="isFocused = false"
        />
        <span class="suffix" v-if="suffix">{{ suffix }}</span>
      </div>
    </div>
  </div>
</template>

<script>
import Slider from '@vueform/slider'

export default {
  components: {
    Slider
  },
  props: ['modelValue', 'label', 'id', 'min', 'max', 'suffix'],
  emits: ['update:modelValue'],
  data() {
    return {
      isFocused: false
    }
  },
  computed: {
    modelMin() {
      return this.min ? Number(this.min) : 0;
    },
    modelMax() {
      return this.max ? Number(this.max) : 1000;
    }
  },
}
</script>

<style src="@vueform/slider/themes/default.css"></style>
<style lang="scss">
  .control-group,
  .control-input-wrapper {
    display: flex;
    align-items: center;
    justify-content: space-between;
    border-radius: var(--border-radius);
  }

  .control-group {
    width: 100%;
    color: var(--text1);
    font-weight: var(--font-weight-medium);
    font-size: var(--font-size-small);

    .control {
      width: 100%;
      display: flex;
      align-items: center;
    }

    .control-slider {
      width: 100%;
      margin: 0 8px 0 24px;
    }

    label {
      width: 100%;
      max-width: 80px;
    }

    .control-input {
      width: 100%;
      max-width: 64px;
      border: none;
      background-color: transparent;
      color: var(--text1);
      padding: 4px 8px;
      --webkit-appearance: none;
      
      &:focus {
        outline: none;
      }
    }

  .control-input::-webkit-outer-spin-button,
  .control-input::-webkit-inner-spin-button {
      /* display: none; <- Crashes Chrome on hover */
      -webkit-appearance: none;
      margin: 0; /* <-- Apparently some margin are still there even though it's hidden */
  }

  .control-input[type=number] {
      -moz-appearance:textfield; /* Firefox */
  }    
  }

  .control-input-wrapper {
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
    max-width: 64px;
    background-color: var(--background4);
    color: var(--text4);
    border: 1px solid var(--border3);

    &.active {
      box-shadow: 0 0 0 1px var(--blueBorder);      
    }

    .suffix{
      display: block;
      font-size: 10px;
      text-transform: uppercase;
      color: var(--textInactive);
      padding-right: 4px;
    }
  }
</style>