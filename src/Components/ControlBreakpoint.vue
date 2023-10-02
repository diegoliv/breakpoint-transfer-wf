<template>
  <div class="control-breakpoints">
    <div
      class="breakpoint-button"
      :v-for="breakpoint in breakpoints"
      :key="breakpoint.value"
    >
      <input 
        type="radio" 
        :name="breakpoint.value" 
        :id="`breakpoint-${breakpoint.value}`"
        :value="breakpoint.value"
        @change="$emit('update:modelValue', $event.target.value)"
      />
      <label
        :for="`breakpoint-${breakpoint.value}`"
      >
      <img :src="`../Icons/breakpoint-${breakpoint.icon}.svg`" />
    </label>
    </div>
  </div>
</template>

<script>
export default {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  data() {
    return {
      breakpoints: [
        {
          name: '1920px and up',
          value: '1920px',
          icon: '1920px'
        }
      ]
    }
  },
}

</script>