<template>
  <div class="control-breakpoints-wrapper">
    <span class="control-breakpoints-label">Pick a breakpoint to transfer</span>
    <div class="control-breakpoints">
      <div
        class="breakpoint-button"
        v-for="breakpoint in breakpoints"
        :key="breakpoint.value"
      >
        <input 
          type="radio" 
          name="breakpoints" 
          :id="`breakpoint-${breakpoint.value}`"
          :value="breakpoint.value"
          :checked="breakpoint.value === modelValue"
          @change="$emit('update:modelValue', breakpoint.value)"
        />
        <label
          :for="`breakpoint-${breakpoint.value}`"
        >
        <img class="breakpoint-icon" :src="breakpoint.icon" />
      </label>
      </div>
    </div>
  </div>
</template>

<script>
import img1920px from "../Icons/breakpoint-1920px.svg";
import img1440px from "../Icons/breakpoint-1440px.svg";
import img1280px from "../Icons/breakpoint-1280px.svg";
import imgDefault from "../Icons/breakpoint-default.svg";
import imgTablet from "../Icons/breakpoint-tablet.svg";
import imgMLandscape from "../Icons/breakpoint-m-landscape.svg";
import imgMPortrait from "../Icons/breakpoint-m-portrait.svg";

export default {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  data() {
    return {
      breakpoints: [
        {
          name: '1920px and up',
          value: '1920px',
          icon: img1920px
        },
        {
          name: '1440px and up',
          value: '1440px',
          icon: img1440px
        },
        {
          name: '1280px and up',
          value: '1280px',
          icon: img1280px
        },
        {
          name: 'Default',
          value: 'default',
          icon: imgDefault
        },
        {
          name: 'Tablet',
          value: 'tablet',
          icon: imgTablet
        },
        {
          name: 'Mobile (landscape)',
          value: 'mobile-landscape',
          icon: imgMLandscape
        },
        {
          name: 'Mobile (portrait)',
          value: 'mobile-portrait',
          icon: imgMPortrait
        }
      ]
    }
  },
}

</script>

<style lang="scss">
.control-breakpoints-wrapper {
  display: flex;
  padding: 8px 8px 12px;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  font-weight: var(--font-weight-medium);
  color: var(--text1);
  border-bottom: 1px solid var(--border3);
}
.control-breakpoints-label {
  width: 100%;
  text-align: left;
}
.control-breakpoints {
  display: grid;
  grid-template-columns: repeat(auto);
  grid-auto-flow: column;
  justify-content: center;
  align-content: center;
  gap: 1px;
  padding: 1px;
  margin-top: 4px;
  background-color: var(--border3);

  .breakpoint-button {
    width: 32px;
    height: 32px;
  }

  input[type="radio"] {
    display: none;

    + label {
      width: 32px;
      height: 32px;
      display: flex;
      align-items: center;
      justify-content: center;
      background-color: var(--actionSecondaryBackground);
      cursor: pointer;
      
      &:hover {
        background-color: var(--actionSecondaryBackgroundHover);

        .breakpoint-icon {
          opacity: 1;
        }
      }
    }

    &:checked {
      + label {
        background-color: var(--actionPrimaryBackground);
      }

      .breakpoint-icon {opacity: 1;}
    }
  }
  .breakpoint-icon{
    width: 24px;
    height: 24px;
    opacity: .8;
  }
}
</style>