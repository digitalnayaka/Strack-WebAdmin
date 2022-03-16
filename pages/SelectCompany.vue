<template>
  <div>
    <center>
        <div style="width:500px;margin-top:20%">
                <h1>List Company</h1>
                <v-text-field
                    outlined
                    v-model="namaPerusahaanTerpilih"
                    label="Choose Company"
                    readonly
                    @click="dialogPerusahaan=true"
                    prepend-inner-icon="mdi-chevron-down"
                ></v-text-field>
                <v-btn
                    width="100%"
                    large
                    class="white--text"
                    color="#305F72"
                    @click="enterCompany()"
                    :disabled="perusahaanTerpilih=='' ? true : false"
                    >
                    ENTER
                </v-btn>
        </div>
    </center>
    <v-dialog v-model="dialogPerusahaan" width="700px" persistent>
        <v-card>
            <div class="pt-6 mx-6">
                <v-text-field
                    outlined
                    label="Cari Perusahaan"
                    prepend-inner-icon="mdi-magnify"
                ></v-text-field>
                <v-card @click="pilihPerusahaan(item)" style="border:1px solid #E4E9EC" v-for="(item,index) in listCompany" :key="index">
                    <v-list :style="index%2==0 ? 'background:#E4E9EC' : ''">
                        <v-list-item>
                            <v-list-item-avatar>
                                <v-img src="/img/logo.png"></v-img>
                            </v-list-item-avatar>
                            <v-list-item-content>
                                <v-list-item-title>
                                    {{ item.company_name }}
                                </v-list-item-title>
                                <v-list-item-subtitle>
                                    {{ item.company_type }}
                                </v-list-item-subtitle>
                            </v-list-item-content>
                        </v-list-item>
                    </v-list>
                </v-card>
                <br>
                <v-card-actions >
                    <v-spacer></v-spacer>
                    <v-btn
                        width="100px"
                        @click="dialogPerusahaan=false"
                    >
                        Batalkan
                    </v-btn>
                    <v-btn
                        color="#305F72"
                        width="100px"
                        class="white--text"
                    >
                        Pilih
                    </v-btn>
                </v-card-actions>
            </div>
        </v-card>
    </v-dialog>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import { mask } from 'vue-the-mask'
import Vue from 'vue'

export default {
  name: 'select-company',
  directives: { mask },
  data: () => ({    
      dialogPerusahaan:false,
      listCompany:[],
      listPerusahaan:[
          {
              nama:'Strack',
              deskripsi:'ini adalah sebuah deskripsi'
          },
          {
              nama:'Strack',
              deskripsi:'ini adalah sebuah deskripsi'
          },
          {
              nama:'Strack',
              deskripsi:'ini adalah sebuah deskripsi'
          },
          {
              nama:'Strack',
              deskripsi:'ini adalah sebuah deskripsi'
          },
      ],
      perusahaanTerpilih:[],
      namaPerusahaanTerpilih:'',
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
      await this.$axios
        .get('/user/webadmin/company', {
          params: {
          },
        //   headers: { Authorization: 'Bearer ' + this.user.token },
        })
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
    async pilihPerusahaan(data){
        this.perusahaanTerpilih = data
        this.namaPerusahaanTerpilih = data.company_name
        this.dialogPerusahaan = false
    },
    async enterCompany(){
        console.log('perusahaan dipilih', this.perusahaanTerpilih)
        this.$cookies.set('company', JSON.stringify(this.perusahaanTerpilih))
        this.$router.push('/dashboard')
    }
  },
  async created() {
    await this.getCompany()
  },
}
</script>

<style scoped>
.v-text-field--outlined >>> fieldset {
  border: 3px solid #305F72;
}
</style>
