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
              outlined
            ></v-text-field>
            <div class="text-left" style="color:#305F72"><b>Password</b></div>
            <v-text-field
              type="password"
              outlined
            ></v-text-field>
            <br><br>
            <v-btn
             width="100%"
             large
             class="white--text"
             color="#305F72"
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
  },
  async created() {
    
  },
}
</script>

<style scoped>
.v-text-field--outlined >>> fieldset {
  border: 2px solid #305F72;
}
</style>
