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
            <strong>Order Status</strong>
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
            @click="dialogPostStatusOrder = true"
          >
            <v-icon>mdi-plus </v-icon> Add Status Order</v-btn
          >
        </v-col>
      </v-row> -->
      <v-toolbar flat>
        <v-text-field
          class="mt-7"
          solo
          style="width:70%"
          label="Cari"
          v-model="pencarian"
          @keyup="getStatusOrder()"
          clearable
          prepend-inner-icon="mdi-magnify"
        ></v-text-field>
        <v-spacer />
        <v-btn
          class="white--text"
          color="#305F72"
          large
          @click="dialogPostStatusOrder = true"
        >
          <v-icon>mdi-plus </v-icon> Add Status Order</v-btn
        >
      </v-toolbar>
      <br>
      <v-card class="mx-3">
        <v-data-table
          :headers="headers"
          :items="listStatusOrder"
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
                  @click="edit(item,'reason')"
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
                @input="getStatusOrder()"
                :length="lengthPage"
                :total-visible="9"
                color="#305F72"
              ></v-pagination>
            </div>
          </v-col>
        </v-row>
      </v-card>
      <br><br>
    </v-card>
    <v-dialog v-model="dialogPostStatusOrder" width="500px" persistent>
        <v-card>
            <center>
                <br>
                <h2>Add Status Order</h2>
                <br><br>
                <div class="text-left ml-6" style="color:#305F72">Status Order Name</div>
                <v-text-field
                  class="mx-6"
                  v-model="namaStatus"
                  outlined
                ></v-text-field>
                <v-card-actions style="margin-left:33%">
                    <v-btn
                        color="#305F72"
                        outlined
                        @click="dialogPostStatusOrder = false, xData()"
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
    <v-dialog v-model="dialogUpdateStatusOrder" width="500px" persistent>
        <v-card>
            <center>
                <br>
                <h2>Update Status Order</h2>
                <br><br>
                <div class="text-left ml-6" style="color:#305F72">Status Order Name</div>
                <v-text-field
                  class="mx-6"
                  v-model="dataStatusOrder.status"
                  outlined
                ></v-text-field>
                <v-card-actions style="margin-left:33%">
                    <v-btn
                        color="#305F72"
                        outlined
                        @click="dialogUpdateStatusOrder = false, xData()"
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
    <v-dialog v-model="dialogHapusStatusOrder" width="500px" persistent>
        <v-card>
            <center>
                <br>
                <v-img width="60px" src="/img/webp/hapus.webp"></v-img>
                <br>
                <div style="color:#305F72;font-weight:bold">
                    Are you sure want to remove this <br>
                    "{{ dataStatusOrder.status }}" data status?
                </div>
                <v-card-actions style="margin-left:33%">
                    <v-btn
                        color="#305F72"
                        outlined
                        @click="dialogHapusStatusOrder = false"
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
    <v-dialog v-model="dialogStatusDetail" fullscreen>
      <v-card>
        <v-toolbar dense flat color="#305F72" dark>
            <v-toolbar-title><b>List Status "{{dataStatusOrder.status}}"</b></v-toolbar-title>
            <v-spacer></v-spacer>
            <v-toolbar-items>
                <v-btn icon @click="dialogStatusDetail = false">
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
              v-model="pencarianStatus"
              clearable
              prepend-inner-icon="mdi-magnify"
            ></v-text-field>
            <v-spacer />
            <v-btn
              large
              color="#305F72"
              class="text-capitalize" dark
              @click="dialogPostReason = true"
              >Add Status</v-btn
            >
          </v-toolbar>
          <br>
          <v-card class="mx-3">
            <v-data-table
              :headers="headersStatus"
              hide-default-footer
              :items="dataReason"
              :search="pencarianStatus"
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
                    @click="editReason(item,'edit')"
                  >
                    mdi-square-edit-outline
                  </v-icon>
                  <v-icon
                    color="red"
                    @click="editReason(item,'hapus')"
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
    <v-dialog v-model="dialogPostReason" width="500px">
      <v-card>
        <v-toolbar dense flat color="#305F72" dark>
            <v-toolbar-title><b>Add Sub Status Order</b></v-toolbar-title>
            <v-spacer></v-spacer>
            <v-toolbar-items>
                <v-btn icon @click="dialogPostReason = false,namaReason=''">
                <v-icon>mdi-close</v-icon>
                </v-btn>
            </v-toolbar-items>
        </v-toolbar>
        <br>
        <v-card class="mx-3">
          <div class="pt-2 mx-4">
            <div style="color:#305F72">Sub Status Order Status</div>
            <v-text-field
              v-model="dataStatusOrder.status"
              outlined
              disabled
              readonly
            ></v-text-field>
            <div style="color:#305F72">Reason</div>
            <v-textarea
              v-model="namaReason"
              outlined
            ></v-textarea>
          </div>
        </v-card>
        <v-card-actions>
          <v-spacer />
            <v-btn
                color="#305F72"
                outlined
                @click="dialogPostReason = false,namaReason=''"
                large
            >
              Cancel
            </v-btn>
            <v-btn
              large
              class="white--text"
              color="#305F72"
              @click="postDataReason()"
            >
              Save
            </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogUpdateReason" width="500px">
      <v-card>
        <v-toolbar dense flat color="#305F72" dark>
            <v-toolbar-title><b>Edit Sub Status Order</b></v-toolbar-title>
            <v-spacer></v-spacer>
            <v-toolbar-items>
                <v-btn icon @click="dialogUpdateReason = false">
                <v-icon>mdi-close</v-icon>
                </v-btn>
            </v-toolbar-items>
        </v-toolbar>
        <br>
        <v-card class="mx-3">
          <div class="pt-2 mx-4">
            <div style="color:#305F72">Sub Status Order Status</div>
            <v-text-field
              v-model="dataStatusOrder.status"
              outlined
              disabled
              readonly
            ></v-text-field>
            <div style="color:#305F72">Reason</div>
            <v-textarea
              v-model="subreason.reason"
              outlined
            ></v-textarea>
          </div>
        </v-card>
        <v-card-actions>
          <v-spacer />
            <v-btn
                color="#305F72"
                outlined
                @click="dialogUpdateReason = false"
                large
            >
              Cancel
            </v-btn>
            <v-btn
              large
              class="white--text"
              color="#305F72"
              @click="editDataReason()"
            >
              Save
            </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogHapusReason" width="500px" persistent>
        <v-card>
            <center>
                <br>
                <v-img width="60px" src="/img/webp/hapus.webp"></v-img>
                <br>
                <div style="color:#305F72;font-weight:bold">
                    Are you sure want to remove this <br>
                    "{{ subreason.reason }}" data reason?
                </div>
                <v-card-actions style="margin-left:33%">
                    <v-btn
                        color="#305F72"
                        outlined
                        @click="dialogHapusReason = false"
                        large
                        >Cancel</v-btn
                    >
                    <v-btn
                    large
                    class="white--text"
                    color="#305F72"
                    @click="hapusDataReason()"
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
    headersStatus: [
      {
        text: 'No',
        value: 'no',
      },
      { text: 'Reason', value: 'reason' },
      { text: 'Action', value: 'action' },
    ],
    dialogPostStatusOrder:false,
    dialogHapusStatusOrder:false,
    dialogUpdateStatusOrder:false,
    dialogStatusDetail:false,
    dialogPostReason:false,
    dialogUpdateReason:false,
    dialogHapusReason:false,
    namaStatus:'',
    listStatusOrder:[],
    pencarian:'',
    dataStatusOrder:{
        id:'',
        status:'',
    },
    subreason:{
        id:'',
        reason:'',
    },
    namaReason:'',
    dataReason:[],
    pencarianStatus:'',
    lengthPage: 0,
    limit: 9,
    offset: 0,
    page:1,
    IdCompany:'',
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
    async getStatusOrder(){
      var params = new URLSearchParams();

      var offset = (this.offset = (this.page - 1) * this.limit);
      params.append("limit", this.limit);
      params.append("offset", offset);
      params.append("search", this.pencarian);
      params.append("id_company", this.IdCompany.id);

      var request = {
        params: params,
        headers: { Authorization: this.DataToken }
      };
      await this.$axios
        .get('/master/v1/mst_order_status', request)
        .then((response) => {
          let { data } = response.data
          this.listStatusOrder = data
          var mod = response.data.count % this.limit;
          var lengthPage = 0;

          lengthPage = (response.data.count - mod) / this.limit;

          if (mod == 0) {
            this.lengthPage = lengthPage;
          } else {
            this.lengthPage = lengthPage + 1;
          } 
          console.log(this.listStatusOrder)
        })
        .catch((error) => {
          let responses = error.response.data
          console.log(responses.api_message)
        })
        // console.log('ihbad')
    },
    async edit(item,action){
        if (action == 'hapus') {
            this.dataStatusOrder.status = item.status
            this.dataStatusOrder.id = item.id
            this.dialogHapusStatusOrder = true
        }
        if (action == 'reason') {
            this.dataStatusOrder.status = item.status
            this.dataStatusOrder.id = item.id
            // this.dialogHapusStatusOrder = true
            this.getReason()
            this.dialogStatusDetail = true
            // console.log('data pick', this.dataOrderReason)
        }
        if (action == 'reason'){
            this.dataStatusOrder.status = item.status
            this.dataStatusOrder.id = item.id
            this.dialogUpdateStatusOrder = true
        }
    },
    async getReason(){
      await this.$axios
        .get('/master/v1/mst_order_reason', {
          params: {
            'id_mst_order_status': this.dataStatusOrder.id,
            'id_company': this.IdCompany.id
          },
        //   headers: { Authorization: 'Bearer ' + this.user.token },
        })
        .then((response) => {
          let { data } = response.data
          this.dataReason = data
          console.log(this.dataReason)
        })
        .catch((error) => {
          let responses = error.response.data
          console.log(responses.api_message)
        })
    },
    async xData(){
        this.dataStatusOrder={
            id:'',
            status:'',
        }
        this.namaStatus = ''
    },
    async postData(){
      let formData = new FormData()

      formData.append('status', this.namaStatus)
      formData.append('id_user', '1')
      formData.append("id_company", this.IdCompany.id);

      await this.$axios
        .post('/master/v1/mst_order_status', formData, {
          headers: { Authorization: this.DataToken }
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogPostStatusOrder = false
          this.getStatusOrder()
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
    async postDataReason(){
      let formData = new FormData()

      formData.append('reason', this.namaReason)
      formData.append('id_mst_order_status', this.dataStatusOrder.id)
      formData.append('id_user', '1')
      formData.append("id_company", this.IdCompany.id);

      await this.$axios
        .post('/master/v1/mst_order_reason', formData, {
          headers: { Authorization: this.DataToken }
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogPostReason = false
          this.getReason()
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
    async editReason(item,action){
      if (action == 'hapus') {
          this.subreason.reason = item.reason
          this.subreason.id = item.id
          this.dialogHapusReason = true
      }else{
          this.subreason.reason = item.reason
          this.subreason.id = item.id
          this.dialogUpdateReason = true
      }
    },
    async editData(){
      let formData = new FormData()

      formData.append('id', this.dataStatusOrder.id)
      formData.append('status', this.dataStatusOrder.status)
      formData.append('updated_by', '1')

      await this.$axios
        .put('/master/v1/mst_order_status', formData, {
          headers: { Authorization: this.DataToken }
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogUpdateStatusOrder = false
          this.getStatusOrder()
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
    async editDataReason(){
      let formData = new FormData()

      formData.append('id', this.subreason.id)
      formData.append('id_mst_order_status', this.dataStatusOrder.id)
      formData.append('reason', this.subreason.reason)
      formData.append('updated_by', '1')

      await this.$axios
        .put('/master/v1/mst_order_reason', formData, {
          headers: { Authorization: this.DataToken }
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogUpdateReason = false
          this.getReason()
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
      console.log('data',this.dataStatusOrder)

      await this.$axios
        .delete("master/v1/mst_order_status", {
          params: {
            id: this.dataStatusOrder.id,
          },
          headers: { Authorization: this.DataToken }
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogHapusStatusOrder = false
          this.getStatusOrder()
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
    async hapusDataReason(){
      console.log('data',this.subreason)

      await this.$axios
        .delete("master/v1/mst_order_reason", {
          params: {
            id: this.subreason.id,
          },
          headers: { Authorization: this.DataToken }
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogHapusReason = false
          this.getReason()
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
    this.DataToken = this.$cookies.get("token");
    this.IdCompany = this.$cookies.get("company");
    await this.getStatusOrder()
    console.log('data prospect', this.dataStatusOrder)
  },
}
</script>

<style scoped>
.v-text-field--outlined >>> fieldset {
  border: 2px solid #305F72;
}
</style>
