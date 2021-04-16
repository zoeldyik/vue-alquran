<template>
  <v-app>
    <v-main id="main">
      <main-page
        @open-surat="open_surat($event)"
        :loading="loading"
      ></main-page>
      <display-surat
        :surat="surat"
        :isOpen="dialog_isOpen"
        @close="dialog_isOpen = false"
      ></display-surat>
    </v-main>
  </v-app>
</template>

<script>
import mainPage from "./components/main-page";
import displaySurat from "./components/display-surat";

export default {
  name: "App",

  components: {
    mainPage,
    displaySurat,
  },

  data() {
    return {
      surat: {},
      dialog_isOpen: false,
      loading: false,
    };
  },

  methods: {
    async open_surat(id) {
      this.loading = true;
      const response = await fetch(
        `https://api.quran.sutanlab.id/surah/${id}`,
        {
          cache: "force-cache",
        }
      );
      const json = await response.json();
      const result = json.data;

      const {
        name: {
          transliteration: { id: judul },
        },
        name: {
          translation: { id: judul_id },
        },
        numberOfVerses: jumlah_ayat,
        verses: ayat,
        preBismillah,
      } = result;

      this.surat = {
        judul,
        judul_id,
        jumlah_ayat,
        ayat,
        preBismillah,
      };

      this.loading = false;
      this.dialog_isOpen = true;
    },
  },
};
</script>

<style>
#main {
  background-image: url("./assets/background.svg");
  background-size: cover;
  height: 100%;
  min-height: 550px;
  display: flex;
  align-items: center;
}

h2,
h3,
p {
  color: #2a3b4d !important;
}
</style>
