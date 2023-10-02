<template>
  <div class="app-wrapper">
    <div class="app-ui" v-show="isElSelected">
      <div class="selected-class-wrapper">
        <div class="selected-class-label">Selected Class</div>
        <div class="selected-class">{{ selectedStyleName }}</div>
      </div>
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
      <div class="app-actions">
        <button 
          class="action-button"
          :class="{success: updated}"
        >{{ buttonLabel }}</button>
      </div>
    </div>
    <NotSelected v-show="!isElSelected" />
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
      selectedStyleName: null,
      selectedBreakpoint: 'default',
      overwriteStyles: false,
      cleanupStyles: false,
      success: false,
      isUpdating: false
    }
  },
  mounted() {
    webflow.subscribe('selectedelement', this.selectedElementCallback);
  },
  computed: {
    buttonLabel() {
      if (this.success) {
        return 'Styles transfered!';
      }

      if (this.isUpdating) {
        return 'Transfering...';
      }

      return 'Transfer Styles'
    }
  },
  methods: {
    async selectedElementCallback(element) {
      if (element && element.styles) {
        const styles = await element.getStyles();

        if (!styles || styles.length === 0) {
          this.isElSelected = false;
          this.selectedStyle = null;
          this.selectedStyleName = null;
          this.selectedEl = null;
          this.hasMultipleClasses = false;
          return;
        }

        if (styles.length > 1) {
          this.isElSelected = false;
          this.selectedStyle = null;
          this.selectedStyleName = null;
          this.selectedEl = null;
          this.hasMultipleClasses = true;
          return;
        }        

        // get first style from the list
        const style = styles[0];

        console.log(style);
        this.selectedStyleName = style.getName();
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
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    background-color: var(--background1);
  }
  .app-wrapper {
    position:relative;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 360px;
    overflow: hidden;
    .controls {
      width: 100%;
      display: grid;
      grid-template-columns: 1fr;
      gap: 8px;
      padding: 8px;
    }

    .selected-class-wrapper {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 8px;
      border-bottom: 1px solid var(--border3);
      font-weight: var(--font-weight-medium);
      color: var(--text1);

      .selected-class-label {
        white-space: nowrap;
        margin-right: 16px;
      }

      .selected-class {
        height: 24px;
        padding: 4px 6px;
        border-radius: 2px;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;        
        border: 1px solid rgba(43, 43, 43, 0.50);
        background: var(--actionPrimaryBackground);
        color: var(--actionPrimaryText);
        box-shadow: 0px 8px 8px -4px rgba(0, 0, 0, 0.25), 0px 4px 4px -2px rgba(0, 0, 0, 0.25);
      }
    }

    .app-actions {
      margin-top: auto;
      padding: 8px;
      width: 100%;

      .action-button {
        display: flex;
        width: 100%;
        padding: 12px 6px;
        justify-content: center;
        align-items: center;
        border-radius: 4px;        
        color: var(--actionPrimaryText);
        font-size: var(--font-size-big);
        background-color: var(--actionPrimaryBackground);
        cursor: pointer;

        &:hover {
          background-color: var(--actionPrimaryBackgroundHover);
        }

        &.success {
          background-color: var(--greenBackground);
        }
      }
    }
  }
</style>