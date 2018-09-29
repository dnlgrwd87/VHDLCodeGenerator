<template>
  <div id="app">
    <section class="hero is-info">
      <div class="hero-body">
        <div class="container">
          <h1 class="title">
            VHDL Source Code Generator
          </h1>
        </div>
      </div>
    </section>
    <VHDLGenerator />

  </div>

</template>

<script>
import VHDLGenerator from "@/components/VHDLGenerator.vue";

export default {
  name: "app",
  components: {
    VHDLGenerator
  },
  data() {
    return {
      file: null
    };
  }
};
</script>

<style lang="scss">
@import "../node_modules/bulma/bulma.sass";

</style>
