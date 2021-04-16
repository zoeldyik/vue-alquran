<template>
  <v-dialog
    id="display-surat"
    :value="isOpen"
    width="100%"
    max-width="730px"
    overlay-opacity="0.8"
    :fullscreen="isFullScreen"
    persistent
    transition="dialog-bottom-transition"
    :surat="surat"
    scrollable
  >
    <v-card class="my-card">
      <!-- component cari ayat -->
      <cari-ayat
        class="cari-ayat"
        :isOpen="is_cari_ayat"
        @cari="scroll($event)"
        @close="close_cari_ayat"
      ></cari-ayat>

      <v-toolbar flat class="my-toolbar" :width="toolbar_width">
        <!-- close btn -->
        <v-btn fab small color="blue" dark elevation="0" @click="close">
          <v-icon small>mdi-close</v-icon>
        </v-btn>

        <v-spacer></v-spacer>

        <v-toolbar-title
          class="text-center d-flex flex-column justify-content-center align-items-center"
        >
          <h3 class="font-weight-bold mt-3">{{ surat.judul }}</h3>
          <p class="caption mt-n1">
            {{ surat.judul_id }} - {{ surat.jumlah_ayat }} ayat
          </p>
        </v-toolbar-title>
        <v-spacer></v-spacer>

        <v-btn
          fab
          small
          text
          color="blue"
          dark
          elevation="0"
          @click="open_cari_ayat"
        >
          <v-icon>mdi-magnify</v-icon>
        </v-btn>
      </v-toolbar>

      <v-card-text flat class="my-card-text" ref="card_text">
        <!-- bismillah -->
        <div class="ayat text-right mb-4 mb-md-10" v-if="surat.preBismillah">
          <p class="mb-2">
            {{ surat.preBismillah.text.arab }}
          </p>

          <v-btn
            depressed
            text
            color="#2A3B4D"
            x-small
            class="mb-2"
            @click="toggle(`pre-teks`)"
          >
            teks
          </v-btn>

          <v-btn
            depressed
            text
            color="blue"
            x-small
            class="mb-2"
            @click="toggle('pre-terjemahan')"
          >
            terjemahan
          </v-btn>

          <!-- text latin -->
          <v-alert
            class="d-none mb-1"
            ref="pre-teks"
            dark
            color="#2A3B4D"
            border="right"
          >
            <p class="mb-0 caption text-left white--text">
              {{ surat.preBismillah.text.transliteration.en }}
            </p>
          </v-alert>

          <!-- terjemahan -->
          <v-alert
            ref="pre-terjemahan"
            class="d-none"
            dark
            color="blue"
            border="right"
          >
            <p class="mb-0 caption text-left white--text">
              {{ surat.preBismillah.translation.id }}
            </p>
          </v-alert>
          <v-divider></v-divider>
        </div>

        <!-- ayat -->
        <div
          class="ayat text-right mb-4 mb-md-10"
          v-for="item in surat.ayat"
          :key="item.number.inSurah"
        >
          <v-badge
            :content="item.number.inSurah"
            :ref="`badge-${item.number.inSurah}`"
            color="blue"
            left
            class="mb-1"
          ></v-badge>

          <p class="mb-2">
            {{ item.text.arab }}
          </p>

          <v-btn
            depressed
            text
            color="#2A3B4D"
            x-small
            class="mb-2"
            @click="toggle(`alert-teks-${item.number.inSurah}`)"
          >
            teks
          </v-btn>

          <v-btn
            depressed
            text
            color="blue"
            x-small
            class="mb-2"
            @click="toggle(`alert-${item.number.inSurah}`)"
          >
            terjemahan
          </v-btn>

          <!-- text latin -->
          <v-alert
            class="d-none mb-1"
            :ref="`alert-teks-${item.number.inSurah}`"
            dark
            color="#2A3B4D"
            border="right"
          >
            <p class="mb-0 caption text-left white--text">
              {{ item.text.transliteration.en }}
            </p>
          </v-alert>

          <!-- terjemahan -->
          <v-alert
            class="d-none"
            :ref="`alert-${item.number.inSurah}`"
            dark
            color="blue"
            border="right"
          >
            <p class="mb-0 caption text-left white--text">
              {{ item.translation.id }}
            </p>
          </v-alert>
          <v-divider></v-divider>
        </div>
      </v-card-text>
    </v-card>
  </v-dialog>
</template>

<script>
import cariAyat from "./cari-ayat";

export default {
  name: "display-surat",
  props: {
    surat: {
      required: true,
      type: Object,
    },
    isOpen: {
      required: true,
      type: Boolean,
    },
  },
  components: {
    cariAyat,
  },

  data() {
    return {
      is_cari_ayat: false,
    };
  },

  computed: {
    isFullScreen() {
      return this.$vuetify.breakpoint.smAndDown;
    },
    toolbar_width() {
      if (this.$vuetify.breakpoint.smAndDown) {
        return `100%`;
      }
      return "730px";
    },
  },
  methods: {
    close() {
      this.$emit("close");
    },

    toggle(data) {
      if (data.includes("pre")) {
        this.$refs[data].$el.classList.toggle("d-none");
        return;
      }

      this.$refs[data][0].$el.classList.toggle("d-none");
    },

    open_cari_ayat() {
      this.is_cari_ayat = true;
    },

    scroll(ayat) {
      const top = this.$refs[`badge-${ayat}`][0].$el.offsetTop - 90;
      this.$refs.card_text.scrollTo({
        top,
        behavior: "smooth",
      });
      setTimeout(() => {
        this.close_cari_ayat();
      }, 200);
    },
    close_cari_ayat() {
      this.is_cari_ayat = false;
    },
  },
};
</script>

<style scoped>
.my-card {
  position: relative !important;
  scroll-behavior: smooth;
}

.my-toolbar {
  position: fixed;
  z-index: 9999;
}

.my-card-text {
  padding-top: 90px !important;
}

.card-cari-ayat {
  position: absolute;
}

p {
  font-size: 31px;
  line-height: 1.4;
}

@media (min-width: 960px) {
  p {
    font-size: 38px;
    line-height: 1.6;
  }
}
</style>
