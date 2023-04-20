<template>
  <header class="sticky top-0 bg-weather-primary shadow-lg">
    <nav
      class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6"
    >
      <RouterLink :to="{ name: 'home' }">
        <div class="flex items-center gap-3">
          <i class="fa-solid fa-sun text-2xl"></i>
          <p class="text-2xl">O clima local</p>
        </div>
      </RouterLink>

      <div class="flex gap-3 flex-1 justify-end">
        <i
          @click="toggleModal"
          class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer"
        ></i>
        <i
          class="fa-solid fa-plus text-2xl hover:text-weather-secondary duration-150 cursor-pointer"
          @click="addCity"
          v-if="route.query.preview"
        ></i>
      </div>

      <BaseModal :modalActive="modalActive" @close-modal="toggleModal">
        <div class="text-black">
          <h1 class="text-2xl mb-1">Sobre:</h1>
          <p class="mb-4">
            O clima local permite que você acompanhe o clima atual e futuro das
            cidades de sua escolha.
          </p>
          <h2 class="text-2xl">Como funciona:</h2>
          <ol class="list-decimal list-inside mb-4">
            <li>Pesquise sua cidade digitando o nome na barra de pesquisa.</li>
            <li>
              Selecione uma cidade nos resultados, isso o levará ao clima atual
              para sua seleção.
            </li>
            <li>
              Rastreie a cidade clicando no ícone "+" no canto superior direito.
              Isso salvará a cidade para ver mais tarde na página inicial.
            </li>
          </ol>

          <h2 class="text-2xl">Removendo uma cidade:</h2>
          <p>
            Se você não deseja mais rastrear uma cidade, basta selecionar a
            cidade na página inicial. No final da página, haverá uma opção para
            deletar a cidade.
          </p>
        </div>
      </BaseModal>
    </nav>
  </header>
</template>
<script setup>
import { v4 as uuidv4 } from 'uuid';
import { RouterLink, useRoute, useRouter } from "vue-router";
import { ref } from "vue";
import BaseModal from "./BaseModal.vue";


const savedCities = ref([]);
const route = useRoute();
const router = useRouter();


const addCity = () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(
      localStorage.getItem("savedCities")
    );
  }

  const locationObj = {
    id: uuidv4(),
    state: route.params.state,
    city: route.params.city,
    coords: {
      lat: route.query.lat,
      lng: route.query.lng,
    },
  };


  savedCities.value.push(locationObj);
  localStorage.setItem(
    "savedCities",
    JSON.stringify(savedCities.value)
  );
  let query = Object.assign({}, route.query);
  delete query.preview;
  query.id = locationObj.id;
  router.replace({ query });
};


const modalActive = ref(null);
const toggleModal = () => {
  modalActive.value = !modalActive.value;
};
</script>
