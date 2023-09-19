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
          @update="$emit('update:modelValue', $event)"
        />
      </div>
      <div class="control-input-wrapper">
        <input 
          class="control-input"
          type="number" 
          :name="id" 
          :id="id"
          :min="modelMin"
          :max="modelMax"
          :value="modelValue"
          @input="$emit('update:modelValue', $event.target.value)"
        />
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
  props: ['modelValue', 'label', 'id', 'min', 'max'],
  emits: ['update:modelValue'],
  mounted() {
    this.sliderValue = Number(this.modelValue)
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
    width: 100%;
    max-width: 64px;
    background-color: var(--background4);
    color: var(--text4);
    border-color: var(--border2);
  }
</style>