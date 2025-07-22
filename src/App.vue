<script setup>
import Header from "./components/Header.vue";
import Footer from "./components/Footer.vue";
import { computed, onMounted } from "vue";
import { ref } from "vue";
import { nextTick } from "vue";
import { watch } from "vue";

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
watch(filteredData, () => {
  updateMap();
});
onMounted(async () => {
  data.value = await getData();
  setupMap();
});
var map;

let markers = [];
function setupMap() {
  map = L.map("map").setView([48.21664832, 16.37190332], 13);
  L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
    maxZoom: 19,
    attribution:
      '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>',
  }).addTo(map);
  updateMap();
}

function updateMap() {
  console.log("updating", markers);
  markers.forEach((marker) => map.removeLayer(marker));
  markers.length = 0;
  var icon = L.divIcon({
    className: "",
    html: `
    <div class="white-bg marker-wrapper">
      <img src="/icons/SW_Piktogramme_Standortzeichen.svg" alt="marker" />
    </div>
  `,
    iconSize: [38, 38],
  });
  filteredData.value.forEach((playground) => {
    const marker = L.marker(
      [playground.geometry.coordinates[1], playground.geometry.coordinates[0]],
      { icon: icon }
    ).addTo(map);
    marker.bindTooltip(
      `
        <p class="tooltip"><strong>${playground.properties.ANL_NAME}</strong></p>
      `
    );
    marker.featureId = playground.id;
    markers.push(marker);
  });

  nextTick(() => {
    if (markers.length > 0) {
      console.log(markers);
      const bounds = L.latLngBounds(markers.map((m) => m.getLatLng()));
      map.fitBounds(bounds, { padding: [50, 50] });
    }
  });
}

function showOnMap(id) {
  var playground = filteredData.value.find(
    (playground) => playground.id === id
  );
  map.setView(
    [playground.geometry.coordinates[1], playground.geometry.coordinates[0]],
    15
  );
}
function startMarkerAnimation(id) {
  var marker = markers.find((marker) => id === marker.featureId);
  marker.getElement().querySelector(".marker-wrapper").classList.add("bounce");
}
function endMarkerAnimation(id) {
  var marker = markers.find((marker) => id === marker.featureId);
  marker
    .getElement()
    .querySelector(".marker-wrapper")
    .classList.remove("bounce");
}
</script>

<template>
  <Header></Header>
  <teleport to="body">
    <main id="content">
      <wm-stage>
        <h1 slot="content">Spielplätze in Wien entdecken</h1>
      </wm-stage>
      <div class="container">
        <div>
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
          <div class="list">
            <wm-list type="row" gap="xs">
              <ul>
                <li v-for="playground in filteredData" class="cursor-pointer">
                  <div
                    @click="showOnMap(playground.id)"
                    @mouseenter="startMarkerAnimation(playground.id)"
                    @mouseleave="endMarkerAnimation(playground.id)"
                  >
                    <strong>{{ playground.properties.ANL_NAME }}</strong>
                    <!--use Regex to remove commas and whitespaces of faulty entries-->
                    <p>
                      {{
                        playground.properties.SPIELPLATZ_DETAIL.replace(
                          /^,+\s*/,
                          ""
                        )
                      }}
                    </p>
                  </div>
                </li>
              </ul></wm-list
            >
          </div>
        </div>
        <div id="map"></div>
      </div>
      <!-- Live-Region -->
      <div role="status" class="wm-h-vh"></div>
    </main>
  </teleport>
  <Footer></Footer>
</template>
<style>
.container {
  display: flex;
  flex-direction: row;
  gap: 2rem;
}
#map {
  width: 44rem;
  height: 31rem;
}

.list {
  max-height: 31rem;
  overflow-y: auto;
}
@media (max-width: 768px) {
  .container {
    flex-direction: column;
  }

  #map {
    width: 100%;
    height: 19rem;
  }

  .list {
    max-height: 19rem;
  }
}
@font-face {
  font-family: "WienerMelangeVF";
  src: url("https://assets.wien.gv.at/theme/202/fonts/WienerMelangeVF.woff2")
    format("woff2");
  font-weight: normal;
  font-style: normal;
  font-display: swap;
}
.leaflet-tooltip {
  font-family: "WienerMelangeVF", sans-serif;
  font-size: 14px;
  color: #333;
}
.cursor-pointer {
  cursor: pointer;
}
.white-bg {
  background-color: white;
  border-radius: 100%;
  aspect-ratio: 1/1;
  z-index: 1000;
}
.leaflet-marker-icon.bounce {
  animation: bounce 0.5s ease-in-out;
}
.bounce {
  width: 38px;
  height: 38px;
  animation: bounce-animation 0.6s ease infinite;
  transform-origin: center bottom;
  display: inline-block;
}

@keyframes bounce-animation {
  0%,
  100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-40px);
  }
}
</style>
