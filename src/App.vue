<template>
  <div class="app-wrapper">
    <div class="app-ui" v-show="isElSelected">
      <Preview 
        :shadow="shadowCSS" 
        :bg-color="bgColor"
        :box-color="boxColor"
        @setbgcolor="setBgColor"
        @setboxcolor="setBoxColor"
        @openpresets="isPresetsModalOpen = true"
      />
      <div class="controls">
        <Control id="angle" label="Light Position" v-model="angle" min="0" max="360" suffix="deg" />
        <Control id="distance" label="Distance" v-model="distance" min="1" max="1000" />
        <Control id="intensity" label="Intensity" v-model="intensity" min="0.1" max="1" />
        <Control id="sharpness" label="Sharpness" v-model="sharpness" min="0.1" max="1" />
        <ColorControl id="color" label="Color" v-model="color" />
      </div>
      <Transition>
        <div 
          class="presets-modal-wrapper"
          v-if="isPresetsModalOpen"
        >
          <div class="presets-modal-backdrop"></div>
          <PresetsModal
            @selected="setPreset"
            @close="isPresetsModalOpen = false"
          />
        </div>
      </Transition>
    </div>
    <NotSelected />
  </div>
</template>

<script>
import { getSmoothShadow } from 'smooth-shadow';

import Preview from './Components/Preview.vue';
import Control from './Components/Control.vue';
import ColorControl from './Components/ColorControl.vue';
import PresetsModal from './Components/PresetsModal.vue';
import NotSelected from "./Components/NotSelected.vue";

export default {
  components: {
    Preview,
    Control,
    ColorControl,
    PresetsModal,
    NotSelected
  },
  data() {
    return {
      isElSelected: false,
      selectedEl: null,
      selectedStyle: null,
      angle: 0,
      intensity: 0.2,
      distance: 500,
      sharpness: 0.9,
      color: { r: 0, g: 0, b: 0, a: 1 },
      bgColor: { r: 213, g: 231, b: 250, a: 1 },
      boxColor: { r: 255, g: 255, b: 255, a: 1 },
      isPresetsModalOpen: false
    }
  },
  mounted() {
    webflow.subscribe('selectedelement', this.selectedElementCallback);
  },
  computed: {
    shadowCSS() {
      const { x, y } = this.degreesToCoordinates(Number(this.angle));
      const { r, g, b } = this.color;
      return getSmoothShadow({
        distance: this.distance,
        intensity: this.intensity,
        sharpness: this.sharpness,
        color: [r,g,b],
        lightPosition: [x, y]
      })      
    }
  },
  methods: {
    async selectedElementCallback(element) {
      if (element) {
        this.isElSelected = true;
        this.selectedEl = element;
        const styles = await element.getStyles();

        if (!styles || styles.length === 0) {
          return;
        }

        // get last style from the list
        const style = styles[styles.length - 1];
        this.selectedStyle = style;
      } else {
        this.isElSelected = false;
        this.selectedEl = null;
        this.selectedStyle = null;
      }
    },
    degreesToCoordinates(degrees) {
      // Convert degrees to radians
      const radians = ((degrees-90) * Math.PI) / 180;

      // Calculate x and y coordinates
      const x = Number(Math.cos(radians).toFixed(2));
      const y = Number(Math.sin(radians).toFixed(2));

      return { x, y };
    },
    scaleValue(value, from, to) {
      const scale = (to[1] - to[0]) / (from[1] - from[0]);
      const capped = Math.min(from[1], Math.max(from[0], value)) - from[0];
      return ~~(capped * scale + to[0]);
    },
    setBgColor(value) {
      this.bgColor = value;
    },
    setBoxColor(value) {
      this.boxColor = value;
    },
    setPreset(preset) {
      this.angle = preset.angle;
      this.intensity = preset.intensity;
      this.distance = preset.distance;
      this.sharpness = preset.sharpness;
      this.color = preset.color;
      this.bgColor = preset.bgColor;
      this.boxColor = preset.boxColor;

      this.isPresetsModalOpen = false;
    }
  },
  watch: {
    async shadowCSS() {
      this.selectedStyle.setProperties({ 'box-shadow': this.shadowCSS });
      await this.selectedStyle.save();
    }
  }
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