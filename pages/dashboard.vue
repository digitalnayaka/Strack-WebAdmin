<template>
  <div>
    <v-row
      style="min-height:100vh;"
    >
      <v-col
        cols="12"
        sm="3"
        class="d-flex align-content-space-between flex-wrap px-6"
      >
        <div style="margin-top: 7%; flex: 0 0 100%" class="mx-2">
          <center>
            <img style="z-index: 1" width="50%" src="/img/strack_logo.png" />
          </center>
          <br />
          <v-menu transition="slide-y-transition" bottom offset-y>
            <template v-slot:activator="{ on, attrs }">
              <div
                width="100%"
                :style="
                  active === 1
                    ? 'background-color:aliceblue;border: 1px solid #305f72;border-radius: 10px;align-items: center;cursor: pointer;'
                    : 'border: 1px solid #305f72;border-radius: 10px;align-items: center;cursor: pointer;'
                "
                class="px-3 py-2 d-flex justify-content-between"
                v-bind="attrs"
                v-on="on"
                @click="active = 1"
              >
                <div class="d-flex" style="align-items: center; width: 90%">
                  <div><img width="35px" src="/img/master_data.png" /></div>
                  <h4 class="ml-3">Master Data</h4>
                </div>
                <v-icon>mdi-chevron-down</v-icon>
              </div>
            </template>
            <v-list class="overflow-auto" height="300">
              <v-list-item
                v-for="(item, i) in masterData"
                :key="i"
                class="list-datamaster"
                :style="
                  masterDataId == item.id
                    ? 'cursor:pointer;background-color:aliceblue'
                    : 'cursor:pointer;'
                "
                @click="pickMasterData(item.id, item.link)"
              >
                <v-list-item-title class="ml-3" style="color: #305f72">{{
                  item.nama
                }}</v-list-item-title>
              </v-list-item>
            </v-list>
          </v-menu>
          <br />
          <div
            width="100%"
            :style="
              active === 2
                ? 'background-color:aliceblue;border: 1px solid #305f72;border-radius: 10px;align-items: center;cursor: pointer;'
                : 'border: 1px solid #305f72;border-radius: 10px;align-items: center;cursor: pointer;'
            "
            class="px-3 py-2 d-flex"
            @click="toLink(2, 'company')"
          >
            <div><img width="35px" src="/img/company_data.png" /></div>
            <h4 class="ml-3">Company Data</h4>
          </div>
          <br />
          <div
            width="100%"
            :style="
              active === 3
                ? 'background-color:aliceblue;border: 1px solid #305f72;border-radius: 10px;align-items: center;cursor: pointer;'
                : 'border: 1px solid #305f72;border-radius: 10px;align-items: center;cursor: pointer;'
            "
            class="px-3 py-2 d-flex"
            @click="toLink(3, 'monitoring')"
          >
            <div><img width="35px" src="/img/webp/monitoring.webp" /></div>
            <h4 class="ml-3">Monitoring Service</h4>
          </div>
        </div>
        <div style="flex: 0 0 100%" class="mx-2">
          <div
            width="100%"
            :style="
              active === 4
                ? 'background-color:aliceblue;border: 1px solid #305f72;border-radius: 10px;align-items: center;cursor: pointer;'
                : 'border: 1px solid #305f72;border-radius: 10px;align-items: center;cursor: pointer;'
            "
            class="px-3 py-2 text-center"
            @click="toLink(4, 'changecompany')"
          >
            <h4 class="ml-3">Change Company</h4>
          </div>
          <br />
          <div
            width="100%"
            style="
              background: #305f72;
              border-radius: 10px;
              align-items: center;
              cursor: pointer;
            "
            class="px-3 py-2 d-flex"
            @click="signOut()"
          >
            <div><img width="25px" src="/img/webp/logout.webp" /></div>
            <h4 class="ml-3 white--text">Logout</h4>
          </div>
        </div>
      </v-col>
      <v-col cols="12" sm="9" style="background: #e4e9ec">
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
    masterData: [
      { id: '1', nama: 'Prospect Status', link: 'prospectstatus' },
      { id: '2', nama: 'Data Source', link: 'datasource' },
      { id: '3', nama: 'Employee Status', link: 'employeestatus' },
      { id: '4', nama: 'Employment Data', link: 'employmentdata' },
      { id: '5', nama: 'Telemarketing Status', link: 'telemarketingstatus' },
      { id: '6', nama: 'Marital Status', link: 'maritalstatus' },
      { id: '7', nama: 'Requirement Data', link: 'requirementdata' },
      { id: '8', nama: 'Religious Data', link: 'religiousdata' },
      { id: '9', nama: 'Place Data', link: 'placedata' },
      { id: '10', nama: 'Visum Status', link: 'visumstatus' },
      { id: '11', nama: 'Order Status', link: 'orderstatus' },
      { id: '12', nama: 'Address Data', link: 'addressdata' },
      { id: '13', nama: 'Address Category', link: 'addresscategory' },
    ],
    masterDataId: '',
    active: '',
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
    signOut() {
      this.setAuth({})
      this.$cookies.set('token', null)
      this.$cookies.set('company', null)
      location.href = '/login'
    },
    async toLink(data, link) {
      this.active = data
      this.$router.push('/dashboard/' + link)
    },
    async pickMasterData(data, link) {
      this.masterDataId = data
      this.$router.push('/dashboard/' + link)
    },
  },
  async created() {
    this.DataToken = this.$cookies.get('token')
    this.IdCompany = this.$cookies.get('company')
    if (this.DataToken == null && this.IdCompany == null) {
      this.$router.push('login')
    } else if (this.DataToken != null && this.IdCompany == null) {
      this.$router.push('selectcompany')
    }
  },
}
</script>

<style scoped>
.v-text-field--outlined >>> fieldset {
  border: 2px solid #305f72;
}
.list-datamaster:hover {
  background-color: aliceblue;
}
</style>
