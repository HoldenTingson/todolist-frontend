<script>
import HomePage from "./components/views/TodoView.vue";

export default {
  components: {
    HomePage,
  },
};
</script>

<template>
  <main>
    <div class="page">
      <HomePage />
    </div>
  </main>
</template>

<style scoped>
@font-face {
  font-family: Sofia Sans;
  src: url("./../src/assets/fonts/sofia-sans-cufonfonts/SofiaSans.ttf");
}

.page {
  padding: 5%;
}
</style>
