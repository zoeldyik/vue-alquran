<template>
  <v-container id="main-page">
    <v-row justify="center" align="center">
      <v-col cols="12" md="6">
        <v-card>
          <v-card-text>
            <h2 class="text-center mb-6 mt-8 mt-md-16">BACA QURAN</h2>
            <v-form ref="form">
              <v-row>
                <v-col cols="12" md="8">
                  <v-autocomplete
                    v-model="value"
                    :items="items"
                    label="CARI SURAT"
                    :rules="rules"
                    color="blue"
                    :menu-props="menu_props"
                  ></v-autocomplete>
                </v-col>
                <v-col cols="12" md="4" class="align-self-center">
                  <v-btn
                    depressed
                    block
                    dark
                    color="blue"
                    @click="open_surat"
                    :loading="loading"
                  >
                    BUKA
                  </v-btn>
                </v-col>
              </v-row>
            </v-form>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: "main-page",
  props: {
    loading: {
      required: true,
      type: Boolean,
    },
  },

  data() {
    return {
      items: ["foo", "bar", "fizz", "buzz"],
      value: null,
      rules: [(v) => !!v || "pilih surat dahulu!"],
      menu_props: {
        maxHeight: 150,
        transition: "scale-transition",
      },
    };
  },

  methods: {
    async getListSurat() {
      try {
        const response = await fetch("https://api.quran.sutanlab.id/surah", {
          cache: "force-cache",
        });
        const json = await response.json();
        const list_surat = json.data;

        this.items = list_surat.map((surat) => {
          return {
            text: surat.name.transliteration.id,
            value: surat.number,
          };
        });
      } catch (err) {
        console.log(`error getlistsurat ${err}`);
      }
    },

    open_surat() {
      if (!this.value) {
        return this.$refs.form.validate();
      }
      this.$emit("open-surat", this.value);
    },
  },

  created() {
    this.getListSurat();
  },
};
</script>

<style scoped></style>
