<script setup>
import Header from "./components/Header.vue";
import Footer from "./components/Footer.vue";
</script>

<template>
  <Header></Header>
  <teleport to="body">
    <main id="content">
      <wm-stage>
        <h1 slot="content">Spielplätze in Wien entdecken</h1>
      </wm-stage>
    </main>
  </teleport>
  <Footer></Footer>
</template>
<style scoped></style>
