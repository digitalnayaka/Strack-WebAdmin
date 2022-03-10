<template>
  <v-container>
    <v-card-title>Marital Status</v-card-title>
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
            @click="dialogPostStatusMarital = true"
          >
            <v-icon>mdi-plus </v-icon> Add Marital Status</v-btn
          >
        </v-col>
      </v-row>
      <v-card class="mx-3">
        <v-data-table
          :headers="headers"
          :items="listStatusMarital"
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
    <v-dialog v-model="dialogPostStatusMarital" width="500px" persistent>
        <v-card>
            <center>
                <br>
                <h2>Add Status Marital</h2>
                <br><br>
                <div class="text-left ml-6" style="color:#305F72">Status Marital Name</div>
                <v-text-field
                  class="mx-6"
                  v-model="namaStatus"
                  outlined
                ></v-text-field>
                <v-card-actions style="margin-left:33%">
                    <v-btn
                        color="#305F72"
                        outlined
                        @click="dialogPostStatusMarital = false, xData()"
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
    <v-dialog v-model="dialogUpdateStatusMarital" width="500px" persistent>
        <v-card>
            <center>
                <br>
                <h2>Update Status Marital</h2>
                <br><br>
                <div class="text-left ml-6" style="color:#305F72">Status Marital Name</div>
                <v-text-field
                  class="mx-6"
                  v-model="dataStatusMarital.name"
                  outlined
                ></v-text-field>
                <v-card-actions style="margin-left:33%">
                    <v-btn
                        color="#305F72"
                        outlined
                        @click="dialogUpdateStatusMarital = false, xData()"
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
    <v-dialog v-model="dialogHapusStatusMarital" width="500px" persistent>
        <v-card>
            <center>
                <br>
                <v-img width="60px" src="/img/webp/hapus.webp"></v-img>
                <br>
                <div style="color:#305F72;font-weight:bold">
                    Are you sure want to remove this <br>
                    "{{ dataStatusMarital.name }}" data status?
                </div>
                <v-card-actions style="margin-left:33%">
                    <v-btn
                        color="#305F72"
                        outlined
                        @click="dialogHapusStatusMarital = false"
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
      { text: 'Status', value: 'name' },
      { text: 'Aksi', value: 'aksi' },
    ],
    dialogPostStatusMarital:false,
    dialogHapusStatusMarital:false,
    dialogUpdateStatusMarital:false,
    namaStatus:'',
    listStatusMarital:[],
    pencarian:'',
    dataStatusMarital:{
        id:'',
        name:'',
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
    async getStatusMarital(){
      await this.$axios
        .get('/master/v1/mst_status_marital', {
          params: {
          },
        //   headers: { Authorization: 'Bearer ' + this.user.token },
        })
        .then((response) => {
          let { data } = response.data
          this.listStatusMarital = data
          console.log(this.listStatusMarital)
        })
        .catch((error) => {
          let responses = error.response.data
          console.log(responses.api_message)
        })
        // console.log('ihbad')
    },
    async edit(item,action){
        if (action == 'hapus') {
            this.dataStatusMarital.name = item.name
            this.dataStatusMarital.id = item.id
            this.dialogHapusStatusMarital = true
        }else{
            this.dataStatusMarital.name = item.name
            this.dataStatusMarital.id = item.id
            this.dialogUpdateStatusMarital = true
        }
    },
    async xData(){
        this.dataStatusMarital={
            id:'',
            status:'',
        }
        this.namaStatus = ''
    },
    async postData(){
      let formData = new FormData()

      formData.append('name', this.namaStatus)
      formData.append('id_user', '1')

      await this.$axios
        .post('/master/v1/mst_status_marital', formData)
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogPostStatusMarital = false
          this.getStatusMarital()
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

      formData.append('id', this.dataStatusMarital.id)
      formData.append('name', this.dataStatusMarital.name)
      formData.append('updated_by', '1')

      await this.$axios
        .put('/master/v1/mst_status_marital', formData)
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogUpdateStatusMarital = false
          this.getStatusMarital()
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
      console.log('data',this.dataStatusMarital)

      await this.$axios
        .delete("master/v1/mst_status_marital", {
          params: {
            id: this.dataStatusMarital.id,
          },
        //   headers: { Authorization: this.DataToken }
        })
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.dialogHapusStatusMarital = false
          this.getStatusMarital()
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
    await this.getStatusMarital()
    console.log('data prospect', this.dataStatusMarital)
  },
}
</script>

<style scoped>
.v-text-field--outlined >>> fieldset {
  border: 2px solid #305F72;
}
</style>
