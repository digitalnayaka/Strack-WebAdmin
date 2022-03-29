<template>
  <div>
    <div class="d-flex">
      <div style="width:40%;background:#305F72">
        <center>
          <img style="z-index:1;width:75%" src="/img/ilustrasi_login.png">
        </center>
      </div>
      <div style="width:60%">
        <center style="margin-top:10%">
          <div style="width:50%;">
            <img style="z-index:1;width:60%" src="/img/logo.png">
            <br><br><br>
            <div class="text-left" style="color:#305F72"><b>Email Addres</b></div>
            <v-text-field
              v-model="email"
              outlined
            ></v-text-field>
            <div class="text-left" style="color:#305F72"><b>Password</b></div>
            <v-text-field
              type="password"
              v-model="password"
              outlined
            ></v-text-field>
            <br><br>
            <v-btn
             width="100%"
             large
             class="white--text"
             color="#305F72"
             @click="login()"
            >
              LOGIN
            </v-btn>
          </div>
        </center>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import { mask } from 'vue-the-mask'
import Vue from 'vue'

export default {
  name: 'login',
  directives: { mask },
  data: () => ({    
    email:'',
    password:'',
    IdCompany:'',
    DataToken:'',
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
    async login(){
      let formData = new FormData()

      formData.append('email', this.email)
      formData.append('password', this.password)

      await this.$axios
        .post('/user/webadmin/login', formData)
        .then((response) => {
          this.setAlert({
            status: true,
            color: 'success',
            text: response.data.api_message,
          })
          this.$cookies.set('token', response.data.token)
          this.$router.push('/selectcompany')
          console.log('data login', response.data.token)
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
    // this.$router.push('/dashboard')
    this.DataToken = this.$cookies.get("token");
    this.IdCompany = this.$cookies.get("company");
    if (this.DataToken != null && this.IdCompany == null) {
      this.$router.push('selectcompany')
    }else if (this.DataToken != null && this.IdCompany != null) {
      this.$router.push('dashboard')
    }
  },
}
</script>

<style scoped>
.v-text-field--outlined >>> fieldset {
  border: 2px solid #305F72;
}
</style>
