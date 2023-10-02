<template>
  <div class="app-wrapper">
    <div class="app-ui" v-show="isElSelected">
      <ControlBreakpoint v-model="selectedBreakpoint" />
      <div class="controls">
        <ControlSwitch 
          id="overwrite" 
          label="Overwrite Styles" 
          description="Replace conflicting properties at your selected breakpoint."
          v-model="overwriteStyles" 
        />
        <ControlSwitch 
          id="cleanup" 
          label="Clean Up Styles" 
          description="Remove transferred styles from the current breakpoint."
          v-model="cleanupStyles" 
        />
      </div>
    </div>
    <NotSelected />
  </div>
</template>

<script>

import ControlSwitch from './Components/ControlSwitch.vue';
import ControlBreakpoint from './Components/ControlBreakpoint.vue';
import NotSelected from "./Components/NotSelected.vue";

export default {
  components: {
    ControlSwitch,
    ControlBreakpoint,
    NotSelected
  },
  data() {
    return {
      isElSelected: false,
      hasMultipleClasses: false,
      selectedEl: null,
      selectedStyle: null,
      selectedBreakpoint: 'default',
      overwriteStyles: false,
      cleanupStyles: false
    }
  },
  mounted() {
    webflow.subscribe('selectedelement', this.selectedElementCallback);
  },
  methods: {
    async selectedElementCallback(element) {
      if (element && element.styles) {
        const styles = await element.getStyles();

        if (!styles || styles.length === 0) {
          this.isElSelected = false;
          this.selectedStyle = null;
          this.selectedEl = null;
          this.hasMultipleClasses = false;
          return;
        }

        if (styles.length > 1) {
          this.isElSelected = false;
          this.selectedStyle = null;
          this.selectedEl = null;
          this.hasMultipleClasses = true;
          return;
        }        

        // get first style from the list
        const style = styles[0];
        this.selectedStyle = style;
        this.isElSelected = true;
        this.hasMultipleClasses = false;
        this.selectedEl = element;
      } else {
        this.isElSelected = false;
        this.selectedEl = null;
        this.selectedStyle = null;
      }
    },
    transferStyles() {
      // this.selectedStyle.setProperties();
      // await this.selectedStyle.save();
    }
  },
}
</script>

<style lang="scss">
  .app-ui {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 460px;
    background-color: var(--background1);
  }
  .app-wrapper {
    position:relative;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 460px;
    overflow: hidden;
    .controls {
      width: 100%;
      flex-grow: 1;
      display: grid;
      grid-template-columns: 1fr;
      gap: 8px;
      padding: 16px;
    }

    .presets-modal-wrapper {
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 24px;
      position: absolute;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      z-index: 30;

      .presets-modal-backdrop {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        z-index: 0;
        background-color: var(--background5);
        opacity: .5;
        transition: opacity ease-in-out .5s;
        transition-delay: 1s;
      }

      .presets-modal {
        z-index: 2;
        transition: all ease .3s;
      }

      &.v-enter-active,
      &.v-leave-active {
        transition: opacity 0.3s ease;
      }

      &.v-enter-from,
      &.v-leave-to {
        opacity: 0;
        .presets-modal {
          transform: translate3d(0, 50%,0) scale(0.9);
          opacity: 0;
        }
      }      
    }
  }
</style>