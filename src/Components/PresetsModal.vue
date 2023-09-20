<template>
  <div class="presets-modal">
    <header>
      <span>Shadow Presets</span>
      <button 
        class="close"
        @click="$emit('close')"
      >
        <svg fill="none" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 16 16">
          <path d="M3.414 2 2 3.414 6.586 8 2 12.586 3.414 14 8 9.414 12.585 14 14 12.585 9.414 8 14 3.415 12.585 2 8 6.586 3.414 2Z" fill="currentColor"/>
        </svg>
      </button>
    </header>
    <ul class="presets-list">
      <li 
        v-for="(preset,key) in presets" 
        :key="key"
      >
      <div 
        class="preset-item"
        :class="preset.mode"
        :style="getBgStyle(preset)"
        @click="$emit('selected', preset)"
      >
        <div 
          class="box-preview"
          :style="getBoxStyle(preset)"
        ></div>
        <div class="preset-label">{{ preset.name }}</div>
      </div>
      </li>
    </ul>
  </div>
</template>

<script>
import { getSmoothShadow } from 'smooth-shadow';

export default {
  data() {
    return {
      presets: [
        {
          name: 'Standard',
          mode: 'light',
          angle: 0,
          distance: 100,
          intensity: 0.1,
          sharpness: 0.5,
          color: { r: 0, g: 0, b: 0, a: 1 },
          bgColor: { r: 213, g: 231, b: 250, a: 1 },
          boxColor: { r: 255, g: 255, b: 255, a: 1 },
        },
        {
          name: 'Short',
          mode: 'light',
          angle: 0,
          distance: 50,
          intensity: 0.15,
          sharpness: 1,
          color: { r: 0, g: 0, b: 0, a: 1 },
          bgColor: { r: 213, g: 231, b: 250, a: 1 },
          boxColor: { r: 255, g: 255, b: 255, a: 1 },
        },
        {
          name: 'Soft',
          mode: 'light',
          angle: 0,
          distance: 80,
          intensity: 0.1,
          sharpness: 0.1,
          color: { r: 0, g: 0, b: 0, a: 1 },
          bgColor: { r: 213, g: 231, b: 250, a: 1 },
          boxColor: { r: 255, g: 255, b: 255, a: 1 },

        },
        {
          name: 'Long',
          mode: 'light',
          angle: 0,
          distance: 500,
          intensity: 0.2,
          sharpness: 0.5,
          color: { r: 0, g: 0, b: 0, a: 1 },
          bgColor: { r: 213, g: 231, b: 250, a: 1 },
          boxColor: { r: 255, g: 255, b: 255, a: 1 },
        },
        {
          name: 'Standard (tint)',
          mode: 'light',
          angle: 0,
          distance: 100,
          intensity: 0.1,
          sharpness: 0.5,
          color: { r: 7, g: 31, b: 87, a: 1 },
          bgColor: { r: 213, g: 231, b: 250, a: 1 },
          boxColor: { r: 255, g: 255, b: 255, a: 1 },
        },
        {
          name: 'Long (tint)',
          mode: 'light',
          angle: 0,
          distance: 500,
          intensity: 0.2,
          sharpness: 0.5,
          color: { r: 10, g: 49, b: 114, a: 1 },
          bgColor: { r: 213, g: 231, b: 250, a: 1 },
          boxColor: { r: 255, g: 255, b: 255, a: 1 },
        },
        {
          name: 'Neon (pink)',
          mode: 'dark',
          angle: 0,
          distance: 60,
          intensity: 0.5,
          sharpness: 0.1,
          color: { r: 226, g: 0, b: 255, a: 1 },
          bgColor: { r: 21, g: 8, b: 31, a: 1 },
          boxColor: { r: 22, g: 8, b: 35, a: 1 },
        },
        {
          name: 'Neon (blue)',
          mode: 'dark',
          angle: 0,
          distance: 80,
          intensity: 1,
          sharpness: 0.1,
          color: { r: 0, g: 60, b: 255, a: 1 },
          bgColor: { r: 8, g: 13, b: 35, a: 1 },
          boxColor: { r: 8, g: 13, b: 35, a: 1 },
        },
      ]
    }
  },
  methods: {
    degreesToCoordinates(degrees) {
      // Convert degrees to radians
      const radians = ((degrees-90) * Math.PI) / 180;

      // Calculate x and y coordinates
      const x = Number(Math.cos(radians).toFixed(2));
      const y = Number(Math.sin(radians).toFixed(2));

      return { x, y };
    },    
    getShadowCSS(preset) {
      const { x, y } = this.degreesToCoordinates(Number(preset.angle));
      const { r, g, b } = preset.color;
      return getSmoothShadow({
        distance: preset.distance,
        intensity: preset.intensity,
        sharpness: preset.sharpness,
        color: [r,g,b],
        lightPosition: [x, y]
      })      
    },
    getBoxStyle(preset) {
      const shadow = this.getShadowCSS(preset);
      const { r, g, b } = preset.boxColor;

      return `background-color: rgba(${r},${g},${b},1); box-shadow: ${shadow}`;
    },
    getBgStyle(preset) {
      const { r, g, b } = preset.bgColor;
      return `background-color: rgba(${r},${g},${b},1)`;      
    }
  }
}
</script>

<style lang="scss">
.presets-modal {
  width: 100%;
  max-height: 100%;
  display: flex;
  flex-direction: column;
  background-color: var(--background1);
  overflow: hidden;
  border-radius: 4px;
  box-shadow: 0px 40px 80px -40px #000, 
              0px 24px 40px -24px rgba(0, 0, 0, 0.25), 
              0px 16px 24px -16px rgba(0, 0, 0, 0.25), 
              0px 4px 8px -4px rgba(0, 0, 0, 0.25);  

  header{
    display: flex;
    flex-shrink: 0;
    align-items: center;
    justify-content: space-between;
    padding: 8px;
    line-height: 1.1;
    font-weight: 600;
    font-size: var(--font-size-large);
    color: var(--text1);
    border-bottom: 1px solid var(--border1);

    .close {
      padding: 0;
      margin: 0;
      background-color: transparent;
      border: none;
      outline: none;
      cursor: pointer;
      color: var(--text3);

      svg {
        width: 16px;
        height: 16px;
      }

      &:hover {
        color: var(--text1);
      }
    }
  }

  .presets-list {
    height: 100%;
    margin: 0;
    padding: 0;
    list-style: none;
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1px;
    overflow: auto;

    li {
      position: relative;
      z-index: 1;
      width: 100%;
      padding-bottom: 100%;

      &:hover{
        z-index: 2;
        .preset-item {
          box-shadow: 0 0 0 1px var(--blueBorder), inset 0 0 0 1px var(--blueBorder);
        }
      }

    }

    .preset-item {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      align-items: center;
      padding: 24px 8px 8px;
      overflow: hidden;
      cursor: pointer;
      box-shadow: 0 0 0 1px var(--border2);

      .box-preview {
        width: 124px;
        height: 124px;
        margin: -32px 0;
        flex-shrink: 0;
        border-radius: 8px;
        transform: scale(.5);
      }

      .preset-label {
        position: relative;
        z-index: 1;
        text-align: center;
        font-family: var(--font-stack);
        font-weight: 700;
        font-size: var(--font-size-small);
        letter-spacing: -0.04em;
        color: rgba(0,0,0,0.5);
      }

      &.dark {
        .preset-label {
          color: rgba(255,255,255,0.7);
        }
      }
    }
  }
}
</style>