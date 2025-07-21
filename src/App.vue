<script setup>
import Header from "./components/Header.vue";
import Footer from "./components/Footer.vue";
import { computed, onMounted } from "vue";
import { ref } from "vue";

const districts = [
  { id: 1, name: "Innere Stadt" },
  { id: 2, name: "Leopoldstadt" },
  { id: 3, name: "Landstraße" },
  { id: 4, name: "Wieden" },
  { id: 5, name: "Margareten" },
  { id: 6, name: "Mariahilf" },
  { id: 7, name: "Neubau" },
  { id: 8, name: "Josefstadt" },
  { id: 9, name: "Alsergrund" },
  { id: 10, name: "Favoriten" },
  { id: 11, name: "Simmering" },
  { id: 12, name: "Meidling" },
  { id: 13, name: "Hietzing" },
  { id: 14, name: "Penzing" },
  { id: 15, name: "Rudolfsheim-Fünfhaus" },
  { id: 16, name: "Ottakring" },
  { id: 17, name: "Hernals" },
  { id: 18, name: "Währing" },
  { id: 19, name: "Döbling" },
  { id: 20, name: "Brigittenau" },
  { id: 21, name: "Floridsdorf" },
  { id: 22, name: "Donaustadt" },
  { id: 23, name: "Liesing" },
];
const data = ref([]);
const selectedDistrict = ref(1);
const getData = async () => {
  const res = await fetch("/data/SPIELPLATZPUNKTOGD.json");
  return await res.json();
};
const filteredData = computed(() => {
  if (!data.value.features) return [];
  return data.value.features.filter(
    (playground) => playground.properties.BEZIRK === selectedDistrict.value
  );
});

onMounted(async () => {
  data.value = await getData();
});
</script>

<template>
  <Header></Header>
  <teleport to="body">
    <main id="content">
      <wm-stage>
        <h1 slot="content">Spielplätze in Wien entdecken</h1>
      </wm-stage>
      <select label="Bezirk" v-model="selectedDistrict">
        <option
          v-for="district in districts"
          :value="district.id"
          :key="district.id"
        >
          {{ district.id }}., {{ district.name }}
        </option>
      </select>
      <p>Gefundene Spielplätze: {{ filteredData.length }}</p>
      <wm-list type="row" gap="xs">
        <ul>
          <li v-for="playground in filteredData">
            <div>
              <strong>{{ playground.properties.ANL_NAME }}</strong>
              <p>{{ playground.properties.SPIELPLATZ_DETAIL }}</p>
            </div>
          </li>
        </ul></wm-list
      >
    </main>
  </teleport>
  <Footer></Footer>
</template>
<style scoped></style>
