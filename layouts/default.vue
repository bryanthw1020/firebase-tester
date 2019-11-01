<template>
  <v-app dark>
    <v-content>
      <v-container>
        <nuxt />
      </v-container>
    </v-content>
    <v-footer :fixed="fixed" app>
      <span>&copy; 2019</span>
    </v-footer>
    <div id="recaptcha-container"></div>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      fixed: false
    };
  }
};
</script>
