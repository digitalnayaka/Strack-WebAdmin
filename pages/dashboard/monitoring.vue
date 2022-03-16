<template>
  <v-container>
    <v-card-title>Service</v-card-title>
    <v-row>
      <v-col cols="12" sm="3" v-for="item in listVm" :key="item.id">
        <v-card >
          <div class="text-center pt-2"><b>{{item.name}}</b></div>
          <div class="text-center"><b>{{item.vm}}</b></div>
          <br>
          <v-row>
            <v-col cols="12" sm="6">
              <center>
                <round-slider
                  class="mx-2"
                  v-model="item.percent_mem"
                  value="50"
                  start-angle="315"
                  end-angle="+270"
                  line-cap="round"
                  read-only="true"
                  radius="40"
                  :tooltip-format="tooltipVal2"
                  width="10"
                  rangeColor="#305F72"
                />
              </center>
            </v-col>
            <v-col cols="12" sm="6">
              <center>
                <round-slider
                  class="mx-2"
                  value="50"
                  v-model="item.percent_storage"
                  start-angle="315"
                  end-angle="+270"
                  line-cap="round"
                  read-only="true"
                  radius="40"
                  :tooltip-format="tooltipVal2"
                  width="10"
                  rangeColor="orange"
                />
              </center>
            </v-col>
          </v-row>
          <div class="d-flex">
            <v-icon x-large color="#20929D">mdi-circle-small</v-icon>
            <div class="mt-2">Memory : {{item.used_mem}} / {{ item.total_mem }}</div>
          </div>
          <div class="d-flex">
            <v-icon x-large color="orange">mdi-circle-small</v-icon>
            <div class="mt-2">Storage : {{item.used_storage}} / {{item.total_storage}}</div>
          </div>
        </v-card>
      </v-col>
    </v-row>
    <br><br>

    <v-card>
      <v-row class="mx-2">
        <v-col cols="12" sm="8">
          <v-text-field
            solo
            label="Cari"
            clearable
            v-model="cariService"
            prepend-inner-icon="mdi-magnify"
          ></v-text-field>
        </v-col>
        <v-col cols="12" sm="4">
          <v-btn
            class="white--text"
            color="#305F72"
            large
            @click="restartAllService()"
          >
            <v-icon>mdi-rotate-3d-variant </v-icon> Restart semua service</v-btn
          >
        </v-col>
      </v-row>
      <v-card class="mx-3">
        <v-data-table
          show-select
          v-model="selected"
          :headers="headers"
          :search="cariService"
          :items="listService"
          hide-default-footer
          :items-per-page="150"
          class="elevation-1"
        >
          <template v-slot:[`item.port`]="{ item }">
            <div>{{ item.ip }} : {{ item.port }}</div>
          </template>
          <template v-slot:[`item.on_terakhir`]="{ item }">
            <div v-if="item.on_terakhir != ''">
              {{
                moment
                  .utc(item.on_terakhir)
                  .add(utc, 'h')
                  .format('DD MMM YYYY, HH:mm')
              }}
              {{ timezone }}
            </div>
          </template>
          <template v-slot:[`item.status`]="{ item }">
            <div v-if="item.status == true" style="color: #20929d">Aktif</div>
            <div v-else style="color: red">Tidak Aktif</div>
          </template>
          <template v-slot:[`item.auto_nyala`]="{ item }">
            <v-switch
              v-model="item.auto_on"
              input-value="true"
              color="#20929D"
              @change="autoNyala(item)"
            ></v-switch>
          </template>
          <template v-slot:[`item.restart`]="{ item }">
            <div>
              <v-btn
                @click="restartService(item)"
                color="#20929d"
                small
                outlined
              >
                <v-icon>mdi-rotate-3d-variant </v-icon> Restart</v-btn
              >
            </div>
          </template>
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
            <div class="pt-1">{{ selected.length }} Service Terpilih</div>
            <v-spacer></v-spacer>

            <div>
              <v-select
                outlined
                :items="action"
                dense
                label="Ubah Service"
                v-model="dataAction"
                single-line
                @change="postAction()"
              ></v-select>
            </div>
          </div>
        </v-col>

        <v-col cols="12" sm="4">
          <v-btn
            @click="restServiceSelected()"
            class="mt-2"
            color="#305F72"
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
    action: ['Aktifkan', 'Non Aktifkan'],
    dataAction: '',
    listService:[],
    listVm:[],
    cariService:'',
    selected:[],
  }),
  computed: {
    ...mapGetters({
      user: 'auth/user',
      prevUrl: 'prevUrl',
      timezone: 'timezone/region',
      utc: 'timezone/utc',
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
    async getService(){
      await this.$axios
        .get('/monitoring/v1/list_service', {
          params: {
          },
        //   headers: { Authorization: 'Bearer ' + this.user.token },
        })
        .then((response) => {
          let { data } = response.data
          this.listService = data
          console.log(this.listService)
        })
        .catch((error) => {
          let responses = error.response.data
          console.log(responses.api_message)
        })
        // console.log('ihbad')
    },
    async getVm(){
      await this.$axios
        .get('/monitoring/v1/list_vm', {
          params: {
          },
        //   headers: { Authorization: 'Bearer ' + this.user.token },
        })
        .then((response) => {
          let { data } = response.data
          this.listVm = data
          console.log(this.listVm)
        })
        .catch((error) => {
          let responses = error.response.data
          console.log(responses.api_message)
        })
        // console.log('ihbad')
    },
    async restartAllService() {
      await this.$axios
        .post('/monitoring/v1/restart_all_service')
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.getService()
          this.getVm()
        })
        .catch((error) => {
          let responses = error.response.data
          this.setAlert({
            status: true,
            color: 'error',
            text: responses.api_message,
          })
        })
    },
    async autoNyala(data){
      let formData = new FormData();
      formData.append('id', data.id);
      formData.append('auto_on', data.auto_on);
      
      await this.$axios
      .post('/monitoring/v1/auto_start', formData)
      .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.getService()
          this.getVm()
      }).catch((error) => {
          let responses = error.response.data
          this.setAlert({
            status: true,
            color: 'error',
            text: responses.api_message,
          })
      });
    },
    async restartService(item) {
      let formData = new FormData();
      formData.append('id', item.id);

      await this.$axios
      .post('/monitoring/v1/restart_service', formData)
      .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.getService()
          this.getVm()
      }).catch((error) => {
          let responses = error.response.data
          this.setAlert({
            status: true,
            color: 'error',
            text: responses.api_message,
          })
      });
    },
    async postAction() {
      if (this.dataAction == 'Aktifkan') {
        let formData = new FormData();
        for (let i = 0; i < this.selected.length; i++) {
          formData.append('id', this.selected[i].id);
        }
        this.$axios
          .post('/monitoring/v1/on_service', formData)
          .then((response) => {
            this.setAlert({
              status: true,
              color: 'success',
              text: response.data.api_message,
            })
            this.getService()
            this.getVm()
            this.dataAction = ''
          })
          .catch((error) => {
            let responses = error.response.data
            this.setAlert({
              status: true,
              color: 'error',
              text: responses.api_message,
            })
          });
      } else {
        let formData = new FormData();
        for (let i = 0; i < this.selected.length; i++) {
          formData.append('id', this.selected[i].id);
        }
        this.$axios
          .post('/monitoring/v1/off_service', formData)
          .then((response) => {
            this.setAlert({
              status: true,
              color: 'success',
              text: response.data.api_message,
            })
            this.getService()
            this.getVm()
            this.dataAction = ''
          })
          .catch((error) => {
            let responses = error.response.data
            this.setAlert({
              status: true,
              color: 'error',
              text: responses.api_message,
            })
          });
      }
    },
    async restServiceSelected() {
      let formData = new FormData();
      for (let i = 0; i < this.selected.length; i++) {
        formData.append('id', this.selected[i].id);
      }
      this.$axios
        .post('/monitoring/v1/restart_service', formData)
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.getService()
          this.getVm()
        })
        .catch((error) => {
          let responses = error.response.data
          this.setAlert({
            status: true,
            color: 'error',
            text: responses.api_message,
          })
        });
    },
  },
  async created() {
    await this.getService()
    await this.getVm()
  },
}
</script>

<style scoped>
.v-text-field--outlined >>> fieldset {
  border: 2px solid #305F72;
}
</style>
