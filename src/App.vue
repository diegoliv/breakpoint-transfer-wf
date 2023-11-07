<template>
  <div class="app-wrapper">
    <div class="app-ui" v-show="isElSelected && !hasMultipleClasses">
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
        <!-- <div>{{ currentBreakpoint }}, {{ overwriteStyles }}, {{ cleanupStyles }}</div> -->
        <!-- <ControlSwitch 
          id="pseudostates" 
          label="Include Pseudo classes / States" 
          description="Transfer styles from pseudo selectors and states (hover, focus, etc)."
          v-model="includeStates" 
        /> -->
      </div>
      <div class="app-actions">
        <button 
          class="action-button"
          :class="{'success': success}"
          :disabled="isUpdating"
          @click="transferStylesAll"
        >{{ buttonLabel }}</button>
      </div>
    </div>
    <NotSelected v-show="!isElSelected && !hasMultipleClasses" />
    <NotAllowed v-show="hasMultipleClasses" />
  </div>
</template>

<script>

import ControlSwitch from './Components/ControlSwitch.vue';
import ControlBreakpoint from './Components/ControlBreakpoint.vue';
import NotSelected from "./Components/NotSelected.vue";
import NotAllowed from "./Components/NotAllowed.vue";

export default {
  components: {
    ControlSwitch,
    ControlBreakpoint,
    NotSelected,
    NotAllowed
  },
  data() {
    return {
      isElSelected: false,
      hasMultipleClasses: false,
      selectedEl: null,
      selectedStyle: null,
      selectedStyleName: null,
      selectedBreakpoint: 'main',
      currentBreakpoint: null,
      overwriteStyles: false,
      cleanupStyles: false,
      includeStates: false,
      success: false,
      isUpdating: false
    }
  },
  mounted() {
    webflow.subscribe('selectedelement', this.selectedElementCallback);
    webflow.subscribe('mediaquery', this.mediaQueryCallback);
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
    mediaQueryCallback(breakpoint) {
      this.currentBreakpoint = breakpoint;
    },
    // transfer styles for a specific (or no) pseudo selector / state
    transferStyles(pseudo) {
      const pseudoState = pseudo || 'noPseudo';

      let properties = this.selectedStyle.getProperties({breakpoint: this.currentBreakpoint, pseudo: pseudoState});
      let clonedProperties = this.cleanupStyles ? JSON.parse(JSON.stringify(properties)) : null;

      console.log('styles for', {breakpoint: this.currentBreakpoint, pseudo: pseudoState}, properties, 'overwrite?', this.overwriteStyles);

      // Check if we want to overwrite styles between breakpoints or not.
      if (!this.overwriteStyles) {
        const targetProperties = this.selectedStyle.getProperties({ breakpoint: this.selectedBreakpoint });

        // iterate over existing properties and exclude conflicting properties.
        Object.keys(properties).forEach(k => {
          if (targetProperties.hasOwnProperty(k)) {
            delete properties[k];
          }
        });
      }

      // if properties is empty, show error
      if (!Object.keys(properties).length) {
        webflow.notify({ type: 'Error', message: 'No properties to transfer for this style on the active breakpoint.' });
        return;
      }

      // console.log('this.selectedStyle', properties, { breakpoint: this.selectedBreakpoint, pseudo: pseudoState });

      // return;

      this.selectedStyle.setProperties(properties, { breakpoint: this.selectedBreakpoint, pseudo: pseudoState });

      // cleanup current breakpoint styles
      if (this.cleanupStyles && clonedProperties) {
        Object.keys(clonedProperties).forEach(k => {
          clonedProperties[k] = '';
        });
        console.log('cleanup', clonedProperties, { breakpoint: this.currentBreakpoint, pseudo: pseudoState });
        this.selectedStyle.setProperties(clonedProperties, { breakpoint: this.currentBreakpoint, pseudo: pseudoState });
      }

    },
    // take care of transfering styles between all pseudo selectors / states (if the user chooses it)
    async transferStylesAll() {
      const pseudoStates = [
        'nth-child(odd)',
        'nth-child(even)',
        'first-child',
        'last-child',
        'hover',
        'active',
        'pressed',
        'visited',
        'focus',
        'focus-visible',
        'focus-within',
        'placeholder',
        'empty',
        'before',
        'after'
      ];

      this.isUpdating = true;

      if (this.includeStates) {
        pseudoStates.forEach(pseudo => {
          this.transferStyles(pseudo);
        })
      } else {
        this.transferStyles();
      }

      await this.selectedStyle.save();

      this.success = true;
      webflow.notify({ type: 'Success', message: 'Properties transfered to target breakpoint!' });
      
      setTimeout(() => {
        this.success = false;
        this.isUpdating = false;
      }, 2000);
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
      border-bottom: 1px solid var(--border1);
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
        border: var(--blueBorder);
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
        padding: 8px 12px;
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