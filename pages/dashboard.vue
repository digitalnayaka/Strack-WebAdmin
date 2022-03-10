<template>
  <div>
    <v-row>
      <v-col cols="12" sm="3">
        <div style="margin-top:10%" class="mx-2">
          <center>
            <img style="z-index:1" src="/img/strack_logo.png">
          </center>
          <br>
          <v-menu
            transition="slide-y-transition"
            bottom
            offset-y
          >
            <template v-slot:activator="{ on, attrs }">
              <div
                width="100%"
                style="border:1px solid #305F72;border-radius:10px"
                class="px-3 py-2 d-flex justify-content-between"
                v-bind="attrs"
                v-on="on"
              >
                <div class="d-flex" style="align-items:center;width:90%">
                  <div><img width="35px" src="/img/master_data.png"></div>
                  <h3 class="ml-3">Master Data</h3>
                </div>
                <v-icon>mdi-chevron-down</v-icon>
              </div>
            </template>
            <v-list>
              <v-list-item
                v-for="(item, i) in masterData"
                :key="i"
                class="list-datamaster"
                :style="masterDataId == item.id ? 'cursor:pointer;background-color:aliceblue' : 'cursor:pointer;'"
                @click="pickMasterData(item.id, item.link)"
              >
                <v-list-item-title class="ml-3" style="color:#305F72">{{ item.nama }}</v-list-item-title>
              </v-list-item>
            </v-list>
          </v-menu>
          <br>
          <div
            width="100%"
            style="border:1px solid #305F72;border-radius:10px;align-items:center;cursor:pointer"
            class="px-3 py-2 d-flex"
            @click="toLink('company')"
          >
            <div><img width="35px" src="/img/company_data.png"></div>
            <h3 class="ml-3">Company Data</h3>
          </div>
          <br>
          <div
            width="100%"
            style="border:1px solid #305F72;border-radius:10px;align-items:center;cursor:pointer"
            class="px-3 py-2 d-flex"
            @click="toLink('monitoring')"
          >
            <div><img width="35px" src="/img/webp/monitoring.webp"></div>
            <h3 class="ml-3">Monitoring Service</h3>
          </div>
          <br><br>
          <div
            width="100%"
            style="border:1px solid #305F72;border-radius:10px;;cursor:pointer"
            class="px-3 py-2 text-center"
            @click="toLink('selectcompany')"
          >
            <h3 class="ml-3">Change Company</h3>
          </div>
          <br>
          <div
            width="100%"
            style="background:#305F72;border-radius:10px;align-items:center;cursor:pointer"
            class="px-3 py-2 d-flex"
          >
            <div><img width="35px" src="/img/webp/logout.webp"></div>
            <h3 class="ml-3 white--text">Logout</h3>
          </div>
        </div>
      </v-col>
      <v-col cols="12" sm="9" style="background:#E4E9EC">
        <NuxtChild :user="user" />
      </v-col>
    </v-row>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import { mask } from 'vue-the-mask'
import Vue from 'vue'

export default {
  name: 'dashboard',
  directives: { mask },
  data: () => ({    
    items: [
      { title: 'Click Me' },
      { title: 'Click Me' },
      { title: 'Click Me' },
      { title: 'Click Me 2' },
    ],
    masterData:[
      {id:'1', nama: 'Prospect Status', link:'prospectstatus'},
      {id:'2', nama: 'Data Source', link:'datasource'},
      {id:'3', nama: 'Employee Status', link:'employeestatus'},
      {id:'4', nama: 'Employment Data', link:'employmentdata'},
      {id:'5', nama: 'Telemarketing Status', link:'telemarketingstatus'},
      {id:'6', nama: 'Marital Status', link:'maritalstatus'},
      {id:'7', nama: 'Requirement Data', link:'requirementdata'},
      {id:'8', nama: 'Religious Data', link:'religiousdata'},
      {id:'9', nama: 'Place Data', link:'placedata'},
      {id:'10', nama: 'Visum Status', link:'visumstatus'},
      {id:'11', nama: 'Order Status', link:'orderstatus'},
      {id:'12', nama: 'Address Data', link:'addressdata'},
      {id:'13', nama: 'Address Category', link:'addresscategory'},
    ],
    masterDataId:'',
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
    async toLink(data){
      if (data == 'selectcompany') {
        this.$router.push('/'+data)
      }else{
        this.$router.push('/dashboard/'+data)
      }
    },
    async pickMasterData(data,link){
      this.masterDataId = data
      this.$router.push('/dashboard/'+link)
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
.list-datamaster:hover{
  background-color: aliceblue;
}
</style>
