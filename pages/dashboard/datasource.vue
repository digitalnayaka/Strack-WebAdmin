<template>
  <v-container>
    <v-card class="pa-5">
      <v-row align="center">
        <v-col cols="12">
          <div>
            <h2
              style="
                font-family: SF Pro Display;
                font-weight: 500;
                color: #5d5f61;
              "
            >
              <strong> Data Source </strong>
            </h2>
          </div>
        </v-col>
      </v-row>
      <br/>
      <!-- <v-row class="mx-2">
        <v-col cols="12" sm="8">
          <v-text-field
            solo
            label="Cari"
            v-model="pencarian"
            clearable
            prepend-inner-icon="mdi-magnify"
          ></v-text-field>
        </v-col>
        <v-col cols="12" sm="4">
          <v-btn
            class="white--text"
            color="#305F72"
            large
            @click="dialogPostDataSource = true"
          >
            <v-icon>mdi-plus </v-icon> Add Data Source</v-btn
          >
        </v-col>
      </v-row> -->
      <v-toolbar flat>
        <v-text-field
          class="mt-7"
          solo
          style="width: 75%"
          label="Cari"
          @keyup="getDataSource()"
          v-model="pencarian"
          clearable
          prepend-inner-icon="mdi-magnify"
        ></v-text-field>
        <v-spacer />
        <v-btn
          class="white--text"
          color="#305F72"
          large
          @click="dialogPostDataSource = true"
        >
          <v-icon>mdi-plus </v-icon> Add Data Source</v-btn
        >
      </v-toolbar>
      <br />
      <v-card class="mx-3">
        <v-data-table
          :headers="headers"
          :items="listDataSource"
          hide-default-footer
          :items-per-page="150"
          class="elevation-1"
        >
          <template v-slot:[`item.no`]="{ index }">
            <div>
              {{ index + 1 }}
            </div>
          </template>
          <template v-slot:[`item.aksi`]="{ item }">
            <v-icon color="#305F72" @click="edit(item, 'edit')">
              mdi-square-edit-outline
            </v-icon>
            <v-icon color="red" @click="edit(item, 'hapus')">
              mdi-delete
            </v-icon>
          </template>
        </v-data-table>
        <v-row>
          <v-col cols="10" md="10">
            <div class="text-center pt-2">
              <v-pagination
                v-model="page"
                @input="getDataSource()"
                :length="lengthPage"
                color="#305F72"
                :total-visible="9"
              ></v-pagination>
            </div>
          </v-col>
        </v-row>
      </v-card>
      <br /><br />
    </v-card>
    <v-dialog v-model="dialogPostDataSource" width="500px" persistent>
      <v-card>
        <center>
          <br />
          <h2>Add Data Source</h2>
          <br /><br />
          <div class="text-left ml-6" style="color: #305f72">
            Data Source Name
          </div>
          <v-text-field
            class="mx-6"
            v-model="namaDataSource"
            outlined
          ></v-text-field>
          <v-card-actions style="margin-left: 33%">
            <v-btn
              color="#305F72"
              outlined
              @click=";(dialogPostDataSource = false), xData()"
              large
              >Cancel</v-btn
            >
            <v-btn
              large
              class="white--text"
              @click="postData()"
              color="#305F72"
            >
              Save
            </v-btn>
          </v-card-actions>
          <br />
        </center>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogUpdateDataSource" width="500px" persistent>
      <v-card>
        <center>
          <br />
          <h2>Update Data Source</h2>
          <br /><br />
          <div class="text-left ml-6" style="color: #305f72">
            Data Source Name
          </div>
          <v-text-field
            class="mx-6"
            v-model="dataDataSource.datasource"
            outlined
          ></v-text-field>
          <v-card-actions style="margin-left: 33%">
            <v-btn
              color="#305F72"
              outlined
              @click=";(dialogUpdateDataSource = false), xData()"
              large
              >Cancel</v-btn
            >
            <v-btn
              large
              class="white--text"
              color="#305F72"
              @click="editData()"
            >
              Save Changes
            </v-btn>
          </v-card-actions>
          <br />
        </center>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogHapusDataSource" width="500px" persistent>
      <v-card>
        <center>
          <br />
          <v-img width="60px" src="/img/webp/hapus.webp"></v-img>
          <br />
          <div style="color: #305f72; font-weight: bold">
            Are you sure want to remove this <br />
            "{{ dataDataSource.datasource }}" data source?
          </div>
          <v-card-actions style="margin-left: 33%">
            <v-btn
              color="#305F72"
              outlined
              @click="dialogHapusDataSource = false"
              large
              >Cancel</v-btn
            >
            <v-btn
              large
              class="white--text"
              color="#305F72"
              @click="hapusData()"
            >
              Yes
            </v-btn>
          </v-card-actions>
          <br />
        </center>
      </v-card>
    </v-dialog>
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
        text: 'No',
        value: 'no',
      },
      { text: 'Status', value: 'datasource' },
      { text: 'Aksi', value: 'aksi' },
    ],
    dialogPostDataSource: false,
    dialogHapusDataSource: false,
    dialogUpdateDataSource: false,
    namaDataSource: '',
    listDataSource: [],
    pencarian: '',
    dataDataSource: {
      id: '',
      datasource: '',
    },

    lengthPage: 0,
    limit: 9,
    offset: 0,
    page: 1,
    IdCompany: '',
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
      return args.value + '%'
    },
    async getDataSource() {
      var params = new URLSearchParams()

      var offset = (this.offset = (this.page - 1) * this.limit)
      params.append('limit', this.limit)
      params.append('offset', offset)
      params.append('id_company', this.IdCompany.id)

      var request = {
        params: params,
        headers: { Authorization: this.DataToken },
      }

      await this.$axios
        .get('/master/v1/mst_data_source', request)
        .then((response) => {
          let { data } = response.data
          this.listDataSource = data
          var mod = response.data.count % this.limit
          var lengthPage = 0

          lengthPage = (response.data.count - mod) / this.limit

          if (mod == 0) {
            this.lengthPage = lengthPage
          } else {
            this.lengthPage = lengthPage + 1
          }
          console.log(this.listDataSource)
        })
        .catch((error) => {
          let responses = error.response.data
          console.log(responses.api_message)
        })
      // console.log('ihbad')
    },
    async edit(item, action) {
      if (action == 'hapus') {
        this.dataDataSource.datasource = item.datasource
        this.dataDataSource.id = item.id
        this.dialogHapusDataSource = true
      } else {
        this.dataDataSource.datasource = item.datasource
        this.dataDataSource.id = item.id
        this.dialogUpdateDataSource = true
      }
    },
    async xData() {
      this.dataDataSource = {
        id: '',
        datasource: '',
      }
      this.namaDataSource = ''
    },
    async postData() {
      let formData = new FormData()

      formData.append('datasource', this.namaDataSource)
      formData.append('id_user', '1')
      formData.append('id_company', this.IdCompany.id)

      await this.$axios
        .post('/master/v1/mst_data_source', formData, {
          headers: { Authorization: this.DataToken },
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogPostDataSource = false
          this.getDataSource()
          this.xData()
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
    async editData() {
      let formData = new FormData()

      formData.append('id', this.dataDataSource.id)
      formData.append('datasource', this.dataDataSource.datasource)
      formData.append('updated_by', '1')

      await this.$axios
        .put('/master/v1/mst_data_source', formData, {
          headers: { Authorization: this.DataToken },
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogUpdateDataSource = false
          this.getDataSource()
          this.xData()
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
    async hapusData() {
      console.log('data', this.dataStatusProspect)

      await this.$axios
        .delete('master/v1/mst_data_source', {
          params: {
            id: this.dataDataSource.id,
          },
          headers: { Authorization: this.DataToken },
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogHapusDataSource = false
          this.getDataSource()
          this.xData()
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
  },
  async created() {
    this.DataToken = this.$cookies.get('token')
    this.IdCompany = this.$cookies.get('company')
    await this.getDataSource()
    console.log('data prospect', this.dataStatusProspect)
  },
}
</script>

<style scoped>
.v-text-field--outlined >>> fieldset {
  border: 2px solid #305f72;
}
</style>
