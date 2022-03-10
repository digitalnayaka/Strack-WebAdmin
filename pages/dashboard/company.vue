<template>
  <v-container>
    <v-card-title>Company Data</v-card-title>
    <br>
    <v-card>
      <v-row class="mx-2">
        <v-col cols="12" sm="8">
          <v-text-field
            solo
            label="Cari"
            clearable
            prepend-inner-icon="mdi-magnify"
          ></v-text-field>
        </v-col>
        <v-col cols="12" sm="4">
          <v-btn
            class="white--text"
            color="#305F72"
            large
          >
            <v-icon>mdi-plus </v-icon> Add Company</v-btn
          >
        </v-col>
      </v-row>
      <v-card class="mx-3">
        <v-data-table
          show-select
          v-model="selected"
          :headers="headers"
          :search="cariService"
          hide-default-footer
          :items-per-page="150"
          class="elevation-1"
        >
        </v-data-table>
      </v-card>
      <br><br>
    </v-card>
    <br><br><br>
    <v-card
      style="position: fixed; bottom: 0;z-index:100"
      class="py-1"
      width="95%"
      elevation="5"
    >
      <v-row class="mx-4">
        <v-spacer></v-spacer>

        <v-col cols="12" sm="4">
          <div class="d-flex pt-2">
            <div class="pt-1">7 Service Terpilih</div>
            <v-spacer></v-spacer>

            <div>
              <v-select
                outlined
                dense
                label="Ubah Service"
                single-line
              ></v-select>
            </div>
          </div>
        </v-col>

        <v-col cols="12" sm="4">
          <v-btn
            class="mt-2"
            color="#20929d"
            outlined
            >Restart</v-btn
          >
        </v-col>
      </v-row>
    </v-card>
  </v-container>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import { mask } from 'vue-the-mask'
// import RoundSlider from 'vue-round-slider'
import Vue from 'vue'

// Vue.component('round-slider', RoundSlider)

export default {
  name: 'monitoring',
  directives: { mask },
  data: () => ({    
    headers: [
      {
        text: 'Nama Service',
        value: 'modul',
      },
      { text: 'Aktivitas Terakhir', value: 'on_terakhir' },
      { text: 'VM', value: 'port' },
      { text: 'Kondisi', value: 'status' },
      { text: 'Aksi', value: 'restart' },
      { text: 'Sistem Cek Terakhir', value: 'cek_status_terakhir' },
      { text: 'Auto Nyala', value: 'auto_nyala' },
    ],
  }),
  computed: {
    ...mapGetters({
      user: 'auth/user',
      prevUrl: 'prevUrl',
    }),
  },
  methods: {
      ...mapActions({
      setAlert: 'alert/set',
      setAuth: 'auth/set',
    }),
    tooltipVal2(args) {
      return args.value + "%";
    },
  },
  async created() {
    
  },
}
</script>

<style scoped>
.v-text-field--outlined >>> fieldset {
  border: 2px solid #305F72;
}
</style>
