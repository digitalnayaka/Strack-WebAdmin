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
              <strong> Company Profile </strong>
            </h2>
          </div>
        </v-col>
      </v-row>
      <br>
      <v-row>
        <v-col cols="12">
          <div >
            <v-list-item class='mx-3' style="vertical-align: top !important">
              <template>
                <v-list-item-avatar
                  style="align-self: start !important"
                  class="pt-0"
                  tile
                  width="100" 
                  height="60"
                >
                  <img 
                    :src="currentCompany[0].company_logo !== '' ? getImage(currentCompany[0].company_logo) : '/img/strack_logo.png'"
                  />
                </v-list-item-avatar>

                <v-list-item-content>
                  <v-list-item-title style="line-height: 1.3em; font-size: 20px">
                    <b>{{ currentCompany[0].company_name }}</b></v-list-item-title
                  >
                  <v-list-item-subtitle style="line-height: 1.3em">{{
                    currentCompany[0].company_type
                  }}</v-list-item-subtitle>

                  <p
                    class="mt-4"
                    style="
                      color: rgba(0, 0, 0, 0.6);
                      font-size: 0.875rem;
                      line-height: 1.3em;
                      text-align: justify;
                    "
                  >
                    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed
                    at lacus dignissim lectus mollis hendrerit. Aenean at odio
                    sagittis, malesuada tellus sed, ultrices lectus. Etiam
                    aliquet tortor ipsum, eu vehicula mauris ornare eu. Nam id
                    tempor urna, nec interdum elit. Nullam viverra iaculis
                    sapien, dignissim rhoncus leo viverra nec. Nullam non velit
                    eu leo tempor blandit non et tortor. Aliquam purus nisi,
                    rutrum vitae blandit malesuada, sagittis eu tortor. Nullam
                    rhoncus felis eu suscipit malesuada. Pellentesque eget arcu
                    pharetra, scelerisque sapien ac, dapibus nulla. Curabitur
                    sit amet auctor enim, nec sagittis massa. Donec ut justo sit
                    amet sapien auctor tempus eget a ipsum. Nulla commodo nibh
                    non risus elementum facilisis. Nulla ac finibus risus. Cras
                    augue felis, tempus id eros vitae, sagittis varius lacus.
                    Integer imperdiet lacinia risus sit amet consectetur. Donec
                    dapibus, nibh a lacinia malesuada, magna est volutpat augue,
                    a rhoncus turpis tortor sed nulla. Ut sed pretium odio.
                    Proin erat purus, posuere a tempor et, convallis eu mauris.
                    Quisque eu lacus eget neque vehicula congue. Aenean felis
                    diam, hendrerit in tincidunt at, varius id arcu.
                  </p>
                </v-list-item-content>
              </template>
            </v-list-item>
          </div>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="12" class="text-center">
          <v-btn
            class="white--text"
            color="#305F72"
            @click="companyDialog = true"
          >
            Change Company</v-btn
          >
        </v-col>
      </v-row>
    </v-card>
    <v-dialog v-model="companyDialog" max-width="700" padding="0" persistent>
      <v-card>
        <v-card-title>Company List</v-card-title>
        <div class="pa-6">
          <v-text-field
            outlined
            v-model="namaPerusahaanTerpilih"
            label="Choose Company"
            readonly
            @click="dialogPerusahaan = true"
            prepend-inner-icon="mdi-chevron-down"
          ></v-text-field>
        </div>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="#305F72"
            outlined
            @click="
              ;(companyDialog = false),
                (perusahaanTerpilih = ''),
                (namaPerusahaanTerpilih = '')
            "
            large
            >Cancel</v-btn
          >
          <v-btn
            @click="enterCompany(), (companyDialog = false)"
            :disabled="perusahaanTerpilih == '' ? true : false"
            large
            class="white--text"
            color="#305F72"
          >
            Change
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogPerusahaan" width="700px" persistent>
      <v-card>
        <div class="pt-6 mx-6">
          <v-card-title class="justify-center">Choose The Company</v-card-title>
          <v-card
            @click="pilihPerusahaan(item)"
            style="border: 1px solid #e4e9ec"
            v-for="(item, index) in listCompany"
            :key="index"
          >
            <v-list :style="index % 2 == 0 ? 'background:#E4E9EC' : ''">
              <v-list-item v-if="item.id !== currentCompany[0].id">
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

              <v-list-item v-else disabled>
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
          <br />
          <v-card-actions class="justify-center">
            <v-btn
              color="#305F72"
              outlined
              large
              class="mb-5"
              @click="dialogPerusahaan = false"
            >
              Cancel
            </v-btn>
          </v-card-actions>
        </div>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script setup>
export default {
  name: 'monitoring',
  data: () => ({
    currentCompany: [],
    listCompany: [],
    companyDialog: false,
    dialogPerusahaan: false,
    perusahaanTerpilih: '',
    namaPerusahaanTerpilih: '',
    DataToken: '',
  }),
  methods: {
    async getCompany() {
      var params = new URLSearchParams()
      var request = {
        params: params,
        headers: { Authorization: this.DataToken },
      }
      await this.$axios
        .get('/user/webadmin/company', request)
        .then((response) => {
          let { data } = response.data
          this.listCompany = data
          console.log('company', this.listCompany)
        })
        .catch((error) => {
          let responses = error.response.data
          console.log(responses.api_message)
        })
    },
    async pilihPerusahaan(data) {
      this.perusahaanTerpilih = data
      this.namaPerusahaanTerpilih = data.company_name
      if (this.perusahaanTerpilih.id === this.currentCompany[0].id) {
        this.perusahaanTerpilih = ''
        this.namaPerusahaanTerpilih = ''
      } else {
        this.dialogPerusahaan = false
      }
    },
    async enterCompany() {
      console.log('perusahaan dipilih', this.perusahaanTerpilih)
      this.$cookies.set('company', JSON.stringify(this.perusahaanTerpilih))
      this.$router.go()
    },
  },
  async created() {
    this.DataToken = this.$cookies.get('token')
    this.currentCompany.push(this.$cookies.get('company'))
    await this.getCompany()
  },
}
</script>

<style lang="scss" scoped>
</style>