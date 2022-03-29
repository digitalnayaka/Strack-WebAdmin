<template>
  <v-container>
    <v-card-title>Company</v-card-title>
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
            @click="dialogPostCompany = true"
          >
            <v-icon>mdi-plus </v-icon> Add Company</v-btn
          >
        </v-col>
      </v-row>
      <v-card class="mx-3">
        <v-data-table
          :headers="headers"
          hide-default-footer
          :items="listCompany"
          :search="pencarian"
          :items-per-page="150"
          class="elevation-1"
        >
            <template v-slot:[`item.no`]="{ index }">
                <div>
                    {{ index+1 }}
                </div>
            </template>
            <template v-slot:[`item.logo`]="{ item }">
                <v-img width="70px" class="my-2" :src="getImage(item.company_logo)"></v-img>
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
    <v-dialog v-model="dialogPostCompany" width="700px" persistent>
        <v-card>
                <br>
                <h2 class="mx-6">Add Company</h2>
                <br>
                <v-card class="mx-6 px-6 pt-3" elevation="1">
                  <div>
                    <h3>Company Information</h3>
                    <v-row>
                      <v-col cols="12" sm="12">
                        <div>Company Name</div>
                        <v-text-field
                          outlined
                          v-model="companyName"
                        >
                        </v-text-field>
                      </v-col>
                    </v-row>
                    <v-row style="margin-top:-30px">
                      <v-col cols="12" sm="6">
                        <div>Company Type</div>
                        <v-autocomplete
                          v-model="pickCompanyType"
                          :items="companyType"
                          outlined
                          item-text="name"
                          height="55px"
                          item-value="id"
                          dense
                        ></v-autocomplete>
                      </v-col>
                      <v-col cols="12" sm="6">
                        <div>Company Logo</div>
                        <v-file-input
                          outlined
                          v-model="companyLogo"
                          :prepend-icon="false"
                          append-icon="mdi-attachment"
                        >
                        </v-file-input>
                      </v-col>
                    </v-row>
                    <h3>Contact Information</h3>
                    <v-row>
                      <v-col cols="12" sm="12">
                        <div>Name</div>
                        <v-text-field
                          outlined
                          v-model="contactName"
                        >
                        </v-text-field>
                      </v-col>
                    </v-row>
                    <v-row style="margin-top:-30px">
                      <v-col cols="12" sm="6">
                        <div>Email Address</div>
                        <v-text-field
                          outlined
                          v-model="contactEmail"
                        >
                        </v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6">
                        <div>Phone Number</div>
                        <v-text-field
                          outlined
                          v-model="contactNumber"
                          prefix="+62 |"
                        >
                        </v-text-field>
                      </v-col>
                    </v-row>
                  </div>
                </v-card>
                <v-card-actions class="mt-3 mr-2">
                  <v-spacer></v-spacer>
                    <v-btn
                        color="#305F72"
                        outlined
                        @click="dialogPostCompany = false,xData()"
                        large
                        >Cancel</v-btn
                    >
                    <v-btn
                    large
                    class="white--text"
                    color="#305F72"
                    @click="postData()"
                    >
                    Save
                    </v-btn>
                </v-card-actions>
                <br>
        </v-card>
    </v-dialog>
    <v-dialog v-model="dialogHapusCompany" width="500px" persistent>
        <v-card>
            <center>
                <br>
                <v-img width="60px" src="/img/webp/hapus.webp"></v-img>
                <br>
                <div style="color:#305F72;font-weight:bold">
                    Are you sure want to remove this <br>
                    "{{ dataCompany.name }}" data company?
                </div>
                <v-card-actions style="margin-left:33%">
                    <v-btn
                        color="#305F72"
                        outlined
                        @click="dialogHapusCompany = false"
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
    <v-dialog v-model="dialogUpdateCompany" width="700px" persistent>
        <v-card>
          <br>
          <h2 class="mx-6">Update Company</h2>
          <br>
          <v-card class="mx-6 px-6 pt-3" elevation="1">
            <div>
              <h3>Company Information</h3>
              <v-row>
                <v-col cols="12" sm="12">
                  <div>Company Name</div>
                  <v-text-field
                    outlined
                    v-model="dataCompany.name"
                  >
                  </v-text-field>
                </v-col>
              </v-row>
              <v-row style="margin-top:-30px">
                <v-col cols="12" sm="6">
                  <div>Company Type</div>
                  <v-autocomplete
                    v-model="dataCompany.type"
                    :items="companyType"
                    outlined
                    item-text="name"
                    height="55px"
                    item-value="id"
                    dense
                  ></v-autocomplete>
                </v-col>
                <v-col cols="12" sm="6">
                  <div>Company Logo</div>
                  <v-file-input
                    outlined
                    v-model="dataCompany.logo"
                    :prepend-icon="false"
                    append-icon="mdi-attachment"
                  >
                  </v-file-input>
                </v-col>
              </v-row>
            </div>
          </v-card>
          <v-card-actions class="mt-3 mr-2">
            <v-spacer></v-spacer>
              <v-btn
                  color="#305F72"
                  outlined
                  @click="dialogUpdateCompany = false,xData()"
                  large
                  >Cancel</v-btn
              >
              <v-btn
              large
              class="white--text"
              color="#305F72"
              @click="editData()"
              >
              Save
              </v-btn>
          </v-card-actions>
          <br>
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
      { text: 'Company Logo', value: 'logo' },
      { text: 'Company Name', value: 'company_name' },
      { text: 'Company Type', value: 'company_type' },
      { text: 'Aksi', value: 'aksi' },
    ],
    pencarian:'',
    companyType:[],
    listCompany:[],
    dialogHapusCompany:false,
    dialogUpdateCompany: false,
    dialogPostCompany:false,
    dataCompany:{
        id:'',
        name:'',
        logo:'',
        type:'',
    },
    companyName:'',
    pickCompanyType:'',
    companyLogo:null,
    contactName:'',
    contactEmail:'',
    contactNumber:'',
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
    async getCompany(){
      var params = new URLSearchParams();
      var request = {
        params: params,
        headers: { Authorization: this.DataToken }
      };
      await this.$axios
        .get('/user/webadmin/company', request)
        .then((response) => {
          let { data } = response.data
          this.listCompany = data
          console.log('company',this.listCompany)
        })
        .catch((error) => {
          let responses = error.response.data
          console.log(responses.api_message)
        })
        // console.log('ihbad')
    },
    async getCompanyType(){
      var params = new URLSearchParams();
      var request = {
        params: params,
        headers: { Authorization: this.DataToken }
      };
      await this.$axios
        .get('/user/webadmin/companytype', request)
        .then((response) => {
          let { data } = response.data
          this.companyType = data
          console.log('company type',this.companyType)
        })
        .catch((error) => {
          let responses = error.response.data
          console.log(responses.api_message)
        })
        // console.log('ihbad')
    },
    async postData(){
      let formData = new FormData()

      formData.append('name', this.companyName)
      formData.append('id_company_type', this.pickCompanyType)
      formData.append('logo', this.companyLogo)
      formData.append('user_name', this.contactName)
      formData.append('email', this.contactEmail)
      formData.append('phone_number', this.contactNumber)

      await this.$axios
        .post('/user/webadmin/company', formData,{
          headers: { Authorization: this.DataToken }
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogPostCompany = false
          this.getCompany()
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
    async xData(){
        this.dataCompany={
            id:'',
            name:'',
        }
        this.companyName=''
        this.pickCompanyType=''
        this.companyLogo=null
        this.contactName=''
        this.contactEmail=''
        this.contactNumber=''
    },
    // async handleNumber(){
    //   let sub
    //   sub = this.phoneNumber.substring(0, 1)
    //   if (sub == '0') {
    //     console.log('dpan',this.phoneNumber)
    //     this.phoneNumber = this.phoneNumber.replace(0, 1)
    //     console.log('baru',this.phoneNumber)
    //   }
    // },
    async edit(item,action){
        if (action == 'hapus') {
            this.dataCompany.name = item.company_name
            this.dataCompany.id = item.id
            this.dataCompany.logo = item.company_logo
            this.dataCompany.type = item.company_type
            this.dialogHapusCompany = true
        }else{
            this.dataCompany.name = item.company_name
            this.dataCompany.id = item.id
            this.dataCompany.logo = item.company_logo
            this.dataCompany.type = item.company_type
            this.dialogUpdateCompany = true
        }
        console.log('data edit',this.dataCompany)
    },
    async editData(){
      let formData = new FormData()

      formData.append('id', this.dataCompany.id)
      formData.append('name', this.dataCompany.name)
      formData.append('type', this.dataCompany.type)
      formData.append('logo', this.dataCompany.logo)
      

      await this.$axios
        .put('/user/webadmin/company', formData,{
          headers: { Authorization: this.DataToken }
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogUpdateCompany = false
          this.getCompany()
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
      console.log('data',this.dataCompany)

      await this.$axios
        .delete("/user/webadmin/company", {
          params: {
            id: this.dataCompany.id,
          },
          headers: { Authorization: this.DataToken }
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogHapusCompany = false
          this.getCompany()
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
    this.DataToken = this.$cookies.get("token");
    this.IdCompany = this.$cookies.get("company");
    await this.getCompany()
    await this.getCompanyType()
  },
}
</script>

<style scoped>
.v-text-field--outlined >>> fieldset {
  border: 2px solid #305F72;
}
</style>
