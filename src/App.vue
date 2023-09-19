<template>
  <div class="app-wrapper">
    <Preview :bg-color="bgColor" :box-color="boxColor" :shadow="shadowCSS" v-if="isElSelected" />
    <div class="controls">
      <Control id="angle" label="Angle" v-model="angle" min="0" max="360" />
      <Control id="distance" label="Distance" v-model="distance" max="1000" />
      <Control id="intensity" label="Intensity" v-model="intensity" max="1" />
      <Control id="sharpness" label="Sharpness" v-model="sharpness" max="1" />
      <ColorControl id="color" label="Color" v-model="color" />
    </div>
    <NotSelected v-if="!isElSelected" />
  </div>
</template>

<script>
import { getSmoothShadow } from 'smooth-shadow';

import Preview from './Components/Preview.vue';
import Control from './Components/Control.vue';
import ColorControl from './Components/ColorControl.vue';
import NotSelected from "./Components/NotSelected.vue";

export default {
  components: {
    Preview,
    Control,
    ColorControl,
    NotSelected
  },
  data() {
    return {
      isElSelected: false,
      selectedEl: null,
      selectedStyle: null,
      bgColor: '#D5E7FA',
      boxColor: '#ffffff',
      angle: 0,
      intensity: 0.2,
      distance: 500,
      sharpness: 0.9,
      color: {
        r: 0,
        g: 0,
        b: 0,
        a: 1
      },
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
  .app-wrapper {
    position:relative;
    display: flex;
    flex-direction: column;
    .controls {
      display: grid;
      grid-template-columns: 1fr;
      gap: 8px;
      padding: 16px;
    }
  }
</style>