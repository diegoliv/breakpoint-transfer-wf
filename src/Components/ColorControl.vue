<template>
  <div class="control-group">
    <label :for="id">{{ label }}</label>
    <div class="control color">
      <div class="color-control">
        <div class="color-trigger" :style="`background-color: ${value}`" @click="togglePicker"></div>
        <div class="color-picker-container" v-show="isActive">
          <Chrome v-model="value"/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Chrome } from "@ckpack/vue-color";

export default {
  components: {
    Chrome
  },
  props: ['modelValue', 'label', 'id'],
  emits: ['update:modelValue'],
  data() {
    return {
      isActive: false,
    }
  },
  computed: {
    value: {
      get() {
        const { r, g, b, a } = this.modelValue;
        return `rgba(${r},${g},${b},${a})`;
      },
      set(value) {
        this.$emit('update:modelValue', value.rgba);
      }
    }    
  },
  methods: {
    togglePicker() {
      this.isActive = !this.isActive;
    },
  }
}
</script>

<style lang="scss">
  .control.color {
    position: relative;
    width: 100%;
    display: flex;
    justify-content: flex-end;

    .color-control {
      position: relative;
    }

    .color-trigger{
      width: 64px;
      height: 32px;
      border-radius: var(--border-radius);
      border: 1px solid var(--border3);
      cursor: pointer;
    }

    .color-picker-container {
      position: absolute;
      bottom: 100%;
      right: 0;
      z-index: 1;
    }
  }
</style>