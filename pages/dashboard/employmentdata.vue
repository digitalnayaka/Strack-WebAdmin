<template>
  <v-container>
    <v-card class="pa-5">
      <v-row>
        <v-col cols="12">
          <h2
            style="
              font-family: SF Pro Display;
              font-weight: 500;
              color: #5d5f61;
            "
          >
            <strong >Employment Data </strong>
          </h2>
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
            @click="dialogPostEmploymentData = true"
          >
            <v-icon>mdi-plus </v-icon> Add Employment Data</v-btn
          >
        </v-col>
      </v-row> -->
      <v-toolbar flat>
        <v-text-field
          class="mt-7"
          solo
          style="width: 70%"
          label="Cari"
          v-model="pencarian"
          @keyup="getEmploymentData()"
          clearable
          prepend-inner-icon="mdi-magnify"
        ></v-text-field>
        <v-spacer />
        <v-btn
          class="white--text"
          color="#305F72"
          large
          @click="dialogPostEmploymentData = true"
        >
          <v-icon>mdi-plus </v-icon> Add Employment Data</v-btn
        >
      </v-toolbar>
      <br />
      <v-card class="mx-3">
        <v-data-table
          :headers="headers"
          :items="listEmploymentData"
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
                @input="getEmploymentData()"
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
    <v-dialog v-model="dialogPostEmploymentData" width="500px" persistent>
      <v-card>
        <center>
          <br />
          <h2>Add Employment Data</h2>
          <br /><br />
          <div class="text-left ml-6" style="color: #305f72">
            Employment Data Name
          </div>
          <v-text-field
            class="mx-6"
            v-model="namaEmployment"
            outlined
          ></v-text-field>
          <v-card-actions style="margin-left: 33%">
            <v-btn
              color="#305F72"
              outlined
              @click=";(dialogPostEmploymentData = false), xData()"
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
    <v-dialog v-model="dialogUpdateEmploymentData" width="500px" persistent>
      <v-card>
        <center>
          <br />
          <h2>Update Employment Data</h2>
          <br /><br />
          <div class="text-left ml-6" style="color: #305f72">
            Employment Data Name
          </div>
          <v-text-field
            class="mx-6"
            v-model="dataEmploymentData.job"
            outlined
          ></v-text-field>
          <v-card-actions style="margin-left: 33%">
            <v-btn
              color="#305F72"
              outlined
              @click=";(dialogUpdateEmploymentData = false), xData()"
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
    <v-dialog v-model="dialogHapusEmploymentData" width="500px" persistent>
      <v-card>
        <center>
          <br />
          <v-img width="60px" src="/img/webp/hapus.webp"></v-img>
          <br />
          <div style="color: #305f72; font-weight: bold">
            Are you sure want to remove this <br />
            "{{ dataEmploymentData.job }}" data employment?
          </div>
          <v-card-actions style="margin-left: 33%">
            <v-btn
              color="#305F72"
              outlined
              @click="dialogHapusEmploymentData = false"
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
      { text: 'Pekerjaan', value: 'job' },
      { text: 'Aksi', value: 'aksi' },
    ],
    dialogPostEmploymentData: false,
    dialogHapusEmploymentData: false,
    dialogUpdateEmploymentData: false,
    namaEmployment: '',
    listEmploymentData: [],
    pencarian: '',
    dataEmploymentData: {
      id: '',
      job: '',
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
    async getEmploymentData() {
      var params = new URLSearchParams()

      var offset = (this.offset = (this.page - 1) * this.limit)
      params.append('limit', this.limit)
      params.append('offset', offset)
      params.append('search', this.pencarian)
      params.append('id_company', this.IdCompany.id)

      var request = {
        params: params,
        headers: { Authorization: this.DataToken },
      }
      await this.$axios
        .get('/master/v1/mst_job', request)
        .then((response) => {
          let { data } = response.data
          this.listEmploymentData = data
          var mod = response.data.count % this.limit
          var lengthPage = 0

          lengthPage = (response.data.count - mod) / this.limit

          if (mod == 0) {
            this.lengthPage = lengthPage
          } else {
            this.lengthPage = lengthPage + 1
          }
          console.log(this.listEmploymentData)
        })
        .catch((error) => {
          let responses = error.response.data
          console.log(responses.api_message)
        })
      // console.log('ihbad')
    },
    async edit(item, action) {
      if (action == 'hapus') {
        this.dataEmploymentData.job = item.job
        this.dataEmploymentData.id = item.id
        this.dialogHapusEmploymentData = true
      } else {
        this.dataEmploymentData.job = item.job
        this.dataEmploymentData.id = item.id
        this.dialogUpdateEmploymentData = true
      }
    },
    async xData() {
      this.dataEmploymentData = {
        id: '',
        status: '',
      }
      this.namaEmployment = ''
    },
    async postData() {
      let formData = new FormData()

      formData.append('job', this.namaEmployment)
      formData.append('id_user', '1')
      formData.append('id_company', this.IdCompany.id)

      await this.$axios
        .post('/master/v1/mst_job', formData, {
          headers: { Authorization: this.DataToken },
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogPostEmploymentData = false
          this.getEmploymentData()
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

      formData.append('id', this.dataEmploymentData.id)
      formData.append('job', this.dataEmploymentData.job)
      formData.append('updated_by', '1')

      await this.$axios
        .put('/master/v1/mst_job', formData, {
          headers: { Authorization: this.DataToken },
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogUpdateEmploymentData = false
          this.getEmploymentData()
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
      console.log('data', this.dataEmploymentData)

      await this.$axios
        .delete('master/v1/mst_job', {
          params: {
            id: this.dataEmploymentData.id,
          },
          headers: { Authorization: this.DataToken },
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogHapusEmploymentData = false
          this.getEmploymentData()
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
    await this.getEmploymentData()
    console.log('data employee', this.dataEmploymentData)
  },
}
</script>

<style scoped>
.v-text-field--outlined >>> fieldset {
  border: 2px solid #305f72;
}
</style>
