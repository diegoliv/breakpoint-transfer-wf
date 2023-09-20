<template>
  <div class="control-group">
    <label :for="id">{{ label }}</label>
    <div class="control color" :class="isActive ? 'active' : ''">
      <div class="color-control" v-click-outside="onClickOutside">
        <div class="color-trigger" :style="`background-color: ${value}`" @click="isActive = !isActive"></div>
        <div class="color-picker-container" v-show="isActive" >
          <Chrome v-model="value"/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Chrome } from "@ckpack/vue-color";
import vClickOutside from 'click-outside-vue3';

export default {
  components: {
    Chrome
  },
  props: ['modelValue', 'label', 'id'],
  emits: ['update:modelValue'],
  directives: {
    clickOutside: vClickOutside.directive
  },
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
    onClickOutside(event) {
      this.isActive = false
    }    
  }
}
</script>

<style lang="scss">
  .control.color {
    position: relative;
    z-index: 10;
    width: 100%;
    display: flex;
    justify-content: flex-end;

    &.active {
      z-index: 20;

      .color-trigger {
        box-shadow: 0 0 0 1px var(--blueBorder);
      }
    }

    .color-control {
      position: relative;
    }

    .color-trigger{
      width: 64px;
      height: 28px;
      border-radius: var(--border-radius);
      border: 1px solid var(--border3);
      cursor: pointer;
    }

    .color-picker-container {
      position: absolute;
      bottom: 100%;
      right: 0;
      z-index: 1;
      padding-bottom: 4px;
    }
  }
</style>