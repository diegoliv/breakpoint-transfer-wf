<template>
  <div class="app-wrapper">
    <Header />
    <NotSelected v-if="!isElSelected" />
  </div>
</template>

<script>
import Header from './Components/Header.vue';
import NotSelected from "./Components/NotSelected.vue";

export default {
  components: {
    Header,
    NotSelected
  },
  data() {
    return {
      isElSelected: false,
      selectedEl: null,
    }
  },
  mounted() {
    webflow.subscribe('selectedelement', this.selectedElementCallback);
  },
  computed: {
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
    }
  }
}
</script>

<style lang="scss">
  .app-wrapper {
    position:relative;
  }
</style>