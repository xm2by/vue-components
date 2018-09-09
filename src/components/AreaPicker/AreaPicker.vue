<template>
  <div class="area-picker">
    <single-area-picker v-if="type === 'single'"></single-area-picker>
    <triple-area-picker v-if="type === 'triple'"></triple-area-picker>
  </div>
</template>

<script>
import SingleAreaPicker from "./SingleAreaPicker";
import TripleAreaPicker from "./TripleAreaPicker";

export default {
  name: "area-picker",
  props: {
    type: {
      type: String,
      default: "triple"
    }
  },
  components: {
    SingleAreaPicker,
    TripleAreaPicker
  }
};
</script>

<style>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>