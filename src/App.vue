<template>
  <div class="app-wrapper">
    <Preview :bg-color="bgColor" :box-color="boxColor" :shadow="shadowCSS" v-if="isElSelected" />
    <div class="controls">
      <Control id="angle" label="Angle" v-model="angle" min="0" max="360" />
      <Control id="blur" label="Blur" v-model="blur" max="200" />
      <Control id="opacity" label="Opacity" v-model="opacity" max="100" />
      <Control id="distance" label="Distance" v-model="distance" />
      <Control id="steps" label="Steps" v-model="steps" max="10" />
    </div>
    <NotSelected v-if="!isElSelected" />
  </div>
</template>

<script>
import Preview from './Components/Preview.vue';
import Control from './Components/Control.vue';
import NotSelected from "./Components/NotSelected.vue";

export default {
  components: {
    Preview,
    Control,
    NotSelected
  },
  data() {
    return {
      isElSelected: false,
      selectedEl: null,
      bgColor: '#D5E7FA',
      boxColor: '#ffffff',
      angle: 180,
      blur: 80,
      distance: 40,
      steps: 3,
      opacity: 50,
    }
  },
  mounted() {
    webflow.subscribe('selectedelement', this.selectedElementCallback);
  },
  computed: {
    shadowCSS() {
      let css = [];

      for (let i = 1; i <= this.steps; i++) {
        const { x, y } = this.degreesToCoordinates(Number(this.angle)),
          distanceX = Number(x * (Number(this.distance) * i / this.steps)),
          distanceY = Number(y * (Number(this.distance) * i / this.steps)),
          blur = (Number(this.blur) * i / this.steps),
          offset = blur / 2,
          opacity = (this.scaleValue(i, [0, this.steps], [0, this.opacity]) * 0.01);

        const color = `rgba(0,0,0,${Number(opacity)})`;

        css.push(`${distanceX}px ${distanceY}px ${blur}px ${color}`);
      }

      console.log(css);

      return css.join(', ');
    }
  },
  methods: {
    selectedElementCallback(element) {
      if (element) {
        this.isElSelected = true;
        this.selectedEl = element;
      } else {
        this.isElSelected = false;
        this.selectedEl = null;
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