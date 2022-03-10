<template>
  <v-container>
    <v-card-title>Telemarketing Status</v-card-title>
    <br>
    <v-card>
      <v-row class="mx-2">
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
            @click="dialogPostTelemarketingStatus = true"
          >
            <v-icon>mdi-plus </v-icon> Add Telemarketing Status</v-btn
          >
        </v-col>
      </v-row>
      <v-card class="mx-3">
        <v-data-table
          :headers="headers"
          :items="listTelemarketingStatus"
          hide-default-footer
          :search="pencarian"
          :items-per-page="150"
          class="elevation-1"
        >
            <template v-slot:[`item.no`]="{ index }">
                <div>
                    {{ index+1 }}
                </div>
            </template>
            <template v-slot:[`item.aksi`]="{ item }">
                <v-icon
                  color="#305F72"
                  @click="edit(item,'edit')"
                >
                  mdi-square-edit-outline
                </v-icon>
                <v-icon
                  color="red"
                  @click="edit(item,'hapus')"
                >
                  mdi-delete
                </v-icon>
            </template>
        </v-data-table>
      </v-card>
      <br><br>
    </v-card>
    <v-dialog v-model="dialogPostTelemarketingStatus" width="500px" persistent>
        <v-card>
            <center>
                <br>
                <h2>Add Telemarketing Status</h2>
                <br><br>
                <div class="text-left ml-6" style="color:#305F72">Telemarketing Status Name</div>
                <v-text-field
                  class="mx-6"
                  v-model="namaStatus"
                  outlined
                ></v-text-field>
                <v-card-actions style="margin-left:33%">
                    <v-btn
                        color="#305F72"
                        outlined
                        @click="dialogPostTelemarketingStatus = false, xData()"
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
                <br>
            </center>
        </v-card>
    </v-dialog>
    <v-dialog v-model="dialogUpdateTelemarketingStatus" width="500px" persistent>
        <v-card>
            <center>
                <br>
                <h2>Update Telemarketing Status</h2>
                <br><br>
                <div class="text-left ml-6" style="color:#305F72">Telemarketing Status Name</div>
                <v-text-field
                  class="mx-6"
                  v-model="dataStatusTelemarketing.status"
                  outlined
                ></v-text-field>
                <v-card-actions style="margin-left:33%">
                    <v-btn
                        color="#305F72"
                        outlined
                        @click="dialogUpdateTelemarketingStatus = false, xData()"
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
                <br>
            </center>
        </v-card>
    </v-dialog>
    <v-dialog v-model="dialogHapusTelemarketingStatus" width="500px" persistent>
        <v-card>
            <center>
                <br>
                <v-img width="60px" src="/img/webp/hapus.webp"></v-img>
                <br>
                <div style="color:#305F72;font-weight:bold">
                    Are you sure want to remove this <br>
                    "{{ dataStatusTelemarketing.status }}" data status?
                </div>
                <v-card-actions style="margin-left:33%">
                    <v-btn
                        color="#305F72"
                        outlined
                        @click="dialogHapusTelemarketingStatus = false"
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
                <br>
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
      { text: 'Status', value: 'status' },
      { text: 'Aksi', value: 'aksi' },
    ],
    dialogPostTelemarketingStatus:false,
    dialogHapusTelemarketingStatus:false,
    dialogUpdateTelemarketingStatus:false,
    namaStatus:'',
    listTelemarketingStatus:[],
    pencarian:'',
    dataStatusTelemarketing:{
        id:'',
        status:'',
    },
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
    async getTelemarketingStatus(){
      await this.$axios
        .get('/master/v1/mst_log_status', {
          params: {
          },
        //   headers: { Authorization: 'Bearer ' + this.user.token },
        })
        .then((response) => {
          let { data } = response.data
          this.listTelemarketingStatus = data
          console.log(this.listTelemarketingStatus)
        })
        .catch((error) => {
          let responses = error.response.data
          console.log(responses.api_message)
        })
        // console.log('ihbad')
    },
    async edit(item,action){
        if (action == 'hapus') {
            this.dataStatusTelemarketing.status = item.status
            this.dataStatusTelemarketing.id = item.id
            this.dialogHapusTelemarketingStatus = true
        }else{
            this.dataStatusTelemarketing.status = item.status
            this.dataStatusTelemarketing.id = item.id
            this.dialogUpdateTelemarketingStatus = true
        }
    },
    async xData(){
        this.dataStatusTelemarketing={
            id:'',
            status:'',
        }
        this.namaStatus = ''
    },
    async postData(){
      let formData = new FormData()

      formData.append('status', this.namaStatus)
      formData.append('id_user', '1')

      await this.$axios
        .post('/master/v1/mst_log_status', formData)
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogPostTelemarketingStatus = false
          this.getTelemarketingStatus()
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
    async editData(){
      let formData = new FormData()

      formData.append('id', this.dataStatusTelemarketing.id)
      formData.append('status', this.dataStatusTelemarketing.status)
      formData.append('updated_by', '1')

      await this.$axios
        .put('/master/v1/mst_log_status', formData)
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogUpdateTelemarketingStatus = false
          this.getTelemarketingStatus()
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
    async hapusData(){
      console.log('data',this.dataStatusTelemarketing)

      await this.$axios
        .delete("master/v1/mst_log_status", {
          params: {
            id: this.dataStatusTelemarketing.id,
          },
        //   headers: { Authorization: this.DataToken }
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogHapusTelemarketingStatus = false
          this.getTelemarketingStatus()
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
    }
  },
  async created() {
    await this.getTelemarketingStatus()
    console.log('data prospect', this.dataStatusTelemarketing)
  },
}
</script>

<style scoped>
.v-text-field--outlined >>> fieldset {
  border: 2px solid #305F72;
}
</style>
