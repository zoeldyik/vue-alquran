<template>
  <v-dialog :value="isOpen" max-width="250" @click:outside="close">
    <v-card class="pt-5">
      <v-card-text>
        <v-form @submit.prevent="cari" ref="form">
          <v-text-field
            label="Nomor ayat"
            :rules="rules"
            v-model="ayat"
          ></v-text-field>
          <v-btn type="submit" block dark color="blue" class="mt-1">
            cari
          </v-btn>
        </v-form>
      </v-card-text>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  props: {
    isOpen: {
      required: true,
      type: Boolean,
    },
  },
  data() {
    return {
      ayat: "",
      rules: [
        (value) => !!value || "tidak boleh kosong",
        (value) => /[0-9]/gi.test(value) || "invalid karakter",
      ],
    };
  },

  methods: {
    close() {
      this.$emit("close");
      this.$refs.form.resetValidation();
    },
    cari() {
      const cek = this.$refs.form.validate();
      if (!cek) return;

      this.$emit("cari", this.ayat);
      this.ayat = "";
      this.$refs.form.resetValidation();
    },
  },
};
</script>

<style scoped></style>
