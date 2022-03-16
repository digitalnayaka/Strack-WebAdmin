<template>
  <v-container>
    <v-card-title>Telemarketing Status</v-card-title>
    <br>
    <v-card>
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
            @click="dialogPostTelemarketingStatus = true"
          >
            <v-icon>mdi-plus </v-icon> Add Telemarketing Status</v-btn
          >
        </v-col>
      </v-row> -->
      <v-toolbar flat>
        <v-text-field
          class="mt-7"
          solo
          style="width:70%"
          label="Cari"
          @keyup="getTelemarketingStatus()"
          v-model="pencarian"
          clearable
          prepend-inner-icon="mdi-magnify"
        ></v-text-field>
        <v-spacer />
        <v-btn
          class="white--text"
          color="#305F72"
          large
          @click="dialogPostTelemarketingStatus = true"
        >
          <v-icon>mdi-plus </v-icon> Add Telemarketing Status</v-btn
        >
      </v-toolbar>
      <br>
      <v-card class="mx-3">
        <v-data-table
          :headers="headers"
          :items="listTelemarketingStatus"
          hide-default-footer
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
                <v-icon
                  color="#305F72"
                  @click="edit(item,'deskripsi')"
                >
                  mdi-menu
                </v-icon>
            </template>
        </v-data-table>
        <v-row>
          <v-col cols="10" md="10">
            <div class="text-center pt-2">
              <v-pagination
                v-model="page"
                color="#305F72"
                @input="getTelemarketingStatus()"
                :length="lengthPage"
                :total-visible="9"
              ></v-pagination>
            </div>
          </v-col>
        </v-row>
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
    <v-dialog v-model="dialogDeskripsi" fullscreen>
      <v-card>
        <v-toolbar dense flat color="#305F72" dark>
            <v-toolbar-title><b>List Status "{{dataStatusTelemarketing.status}}"</b></v-toolbar-title>
            <v-spacer></v-spacer>
            <v-toolbar-items>
                <v-btn icon @click="dialogDeskripsi = false">
                <v-icon>mdi-close</v-icon>
                </v-btn>
            </v-toolbar-items>
        </v-toolbar>
        <br>
        <v-card class="mx-6">
          <v-toolbar flat>
            <v-text-field
              class="mt-7"
              solo
              style="width:80%"
              label="Cari"
              v-model="pencarianDeskripsi"
              clearable
              prepend-inner-icon="mdi-magnify"
            ></v-text-field>
            <v-spacer />
            <v-btn
              large
              color="#305F72"
              @click="dialogPostDeskripsi = true"
              class="text-capitalize" dark
              >Add Description</v-btn
            >
          </v-toolbar>
          <br>
          <v-card class="mx-3">
            <v-data-table
              :headers="headersDeskripsi"
              hide-default-footer
              :search="pencarianDeskripsi"
              :items="dataDeskripsi"
              :items-per-page="150"
              class="elevation-1"
            >
              <template v-slot:[`item.no`]="{ index }">
                  <div>
                      {{ index+1 }}
                  </div>
              </template>
              <template v-slot:[`item.action`]="{ item }">
                  <v-icon
                    color="#305F72"
                    @click="editDescription(item,'edit')"
                  >
                    mdi-square-edit-outline
                  </v-icon>
                  <v-icon
                    color="red"
                    @click="editDescription(item,'hapus')"
                  >
                    mdi-delete
                  </v-icon>
              </template>
            </v-data-table>
          </v-card>
          <br>
        </v-card>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogPostDeskripsi" width="500px">
      <v-card>
        <v-toolbar dense flat color="#305F72" dark>
            <v-toolbar-title><b>Add Sub Status Telemarketing</b></v-toolbar-title>
            <v-spacer></v-spacer>
            <v-toolbar-items>
                <v-btn icon @click="dialogPostDeskripsi = false,namaDeskripsi=''">
                <v-icon>mdi-close</v-icon>
                </v-btn>
            </v-toolbar-items>
        </v-toolbar>
        <br>
        <v-card class="mx-3">
          <div class="pt-2 mx-4">
            <div style="color:#305F72">Sub Status Telemarketing Name</div>
            <v-text-field
              v-model="dataStatusTelemarketing.status"
              outlined
              disabled
              readonly
            ></v-text-field>
            <div style="color:#305F72">Description</div>
            <v-textarea
              v-model="namaDeskripsi"
              outlined
            ></v-textarea>
          </div>
        </v-card>
        <v-card-actions>
          <v-spacer />
            <v-btn
                color="#305F72"
                outlined
                @click="dialogPostDeskripsi = false,namaDeskripsi=''"
                large
            >
              Cancel
            </v-btn>
            <v-btn
              large
              class="white--text"
              color="#305F72"
              @click="postDataDeskripsi()"
            >
              Save
            </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogUpdateDeskripsi" width="500px">
      <v-card>
        <v-toolbar dense flat color="#305F72" dark>
            <v-toolbar-title><b>Add Sub Status Telemarketing</b></v-toolbar-title>
            <v-spacer></v-spacer>
            <v-toolbar-items>
                <v-btn icon @click="dialogUpdateDeskripsi = false">
                <v-icon>mdi-close</v-icon>
                </v-btn>
            </v-toolbar-items>
        </v-toolbar>
        <br>
        <v-card class="mx-3">
          <div class="pt-2 mx-4">
            <div style="color:#305F72">Edit Status Telemarketing Name</div>
            <v-text-field
              v-model="dataStatusTelemarketing.status"
              outlined
              disabled
              readonly
            ></v-text-field>
            <div style="color:#305F72">Description</div>
            <v-textarea
              v-model="subdescription.description"
              outlined
            ></v-textarea>
          </div>
        </v-card>
        <v-card-actions>
          <v-spacer />
            <v-btn
                color="#305F72"
                outlined
                @click="dialogUpdateDeskripsi = false"
                large
            >
              Cancel
            </v-btn>
            <v-btn
              large
              class="white--text"
              color="#305F72"
              @click="editDataDeskripsi()"
            >
              Save
            </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogHapusDeskripsi" width="500px" persistent>
        <v-card>
            <center>
                <br>
                <v-img width="60px" src="/img/webp/hapus.webp"></v-img>
                <br>
                <div style="color:#305F72;font-weight:bold">
                    Are you sure want to remove this <br>
                    "{{ subdescription.description }}" data description?
                </div>
                <v-card-actions style="margin-left:33%">
                    <v-btn
                        color="#305F72"
                        outlined
                        @click="dialogHapusDeskripsi = false"
                        large
                        >Cancel</v-btn
                    >
                    <v-btn
                    large
                    class="white--text"
                    color="#305F72"
                    @click="hapusDataDeskripsi()"
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
    headersDeskripsi: [
      {
        text: 'No',
        value: 'no',
      },
      { text: 'Description', value: 'description' },
      { text: 'Action', value: 'action' },
    ],
    dialogPostTelemarketingStatus:false,
    dialogHapusTelemarketingStatus:false,
    dialogUpdateTelemarketingStatus:false,
    dialogDeskripsi:false,
    dialogPostDeskripsi:false,
    dialogUpdateDeskripsi:false,
    dialogHapusDeskripsi:false,
    namaStatus:'',
    listTelemarketingStatus:[],
    pencarian:'',
    dataStatusTelemarketing:{
        id:'',
        status:'',
    },
    dataDeskripsi:[],
    subdescription:{
      id:'',
      description:'',
    },
    namaDeskripsi:'',
    pencarianDeskripsi:'',

    lengthPage: 0,
    limit: 9,
    offset: 0,
    page:1,
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
      var params = new URLSearchParams();

      var offset = (this.offset = (this.page - 1) * this.limit);
      params.append("limit", this.limit);
      params.append("offset", offset);
      params.append("search", this.pencarian);

      var request = {
        params: params,
        // headers: { Authorization: this.DataToken }
      };
      await this.$axios
        .get('/master/v1/mst_log_status', request)
        .then((response) => {
          let { data } = response.data
          this.listTelemarketingStatus = data
          var mod = response.data.count % this.limit;
          var lengthPage = 0;

          lengthPage = (response.data.count - mod) / this.limit;

          if (mod == 0) {
            this.lengthPage = lengthPage;
          } else {
            this.lengthPage = lengthPage + 1;
          } 
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
        }if (action == 'deskripsi') {
            this.dataStatusTelemarketing.status = item.status
            this.dataStatusTelemarketing.id = item.id
            this.getDeskripsi()
            this.dialogDeskripsi = true
        }else{
            this.dataStatusTelemarketing.status = item.status
            this.dataStatusTelemarketing.id = item.id
            this.dialogUpdateTelemarketingStatus = true
        }
    },
    async getDeskripsi(){
      await this.$axios
        .get('/master/v1/mst_log_desc', {
          params: {
            'id_mst_tele_status': this.dataStatusTelemarketing.id
          },
        //   headers: { Authorization: 'Bearer ' + this.user.token },
        })
        .then((response) => {
          let { data } = response.data
          this.dataDeskripsi = data
          console.log(this.dataDeskripsi)
        })
        .catch((error) => {
          let responses = error.response.data
          console.log(responses.api_message)
        })
    },
    async editDescription(item,action){
      if (action == 'hapus') {
          this.subdescription.description = item.description
          this.subdescription.id = item.id
          this.dialogHapusDeskripsi = true
      }else{
          this.subdescription.description = item.description
          this.subdescription.id = item.id
          this.dialogUpdateDeskripsi = true
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
    async postDataDeskripsi(){
      let formData = new FormData()

      formData.append('description', this.namaDeskripsi)
      formData.append('id_mst_log_status', this.dataStatusTelemarketing.id)
      formData.append('id_user', '1')

      await this.$axios
        .post('/master/v1/mst_log_desc', formData)
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogPostDeskripsi = false
          this.namaDeskripsi = ''
          this.getDeskripsi()
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
    async editDataDeskripsi(){
      let formData = new FormData()

      formData.append('description', this.subdescription.description)
      formData.append('id', this.subdescription.id)
      formData.append('id_mst_log_status', this.dataStatusTelemarketing.id)
      formData.append('updated_by', '1')

      await this.$axios
        .put('/master/v1/mst_log_desc', formData)
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogUpdateDeskripsi = false
          this.getDeskripsi()
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
    },
    async hapusDataDeskripsi(){
      console.log('data',this.subdescription)

      await this.$axios
        .delete("master/v1/mst_log_desc", {
          params: {
            id: this.subdescription.id,
          },
        //   headers: { Authorization: this.DataToken }
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogHapusDeskripsi = false
          this.getDeskripsi()
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
