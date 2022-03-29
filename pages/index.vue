<template>
  <div>
    
  </div>
</template>

<script>

import { mapGetters, mapActions } from 'vuex'


export default {
  name: 'Home',
  components: {
        
  },
  data: () => ({
    
  }),
  computed: {
    ...mapGetters({
      utc: 'timezone/utc',
      timezone: 'timezone/region',
    }),
  },
  methods: {
    ...mapActions({
      setSellertId: 'product/setSeller',
      setProductId: 'product/setProduct',
    }),

  },
  async created() {
    this.DataToken = this.$cookies.get("token");
    this.IdCompany = this.$cookies.get("company");
    if (this.DataToken != null && this.IdCompany == null) {
      this.$router.push('selectcompany')
    }else if (this.DataToken != null && this.IdCompany != null) {
      this.$router.push('dashboard')
    }else if (this.DataToken == null && this.IdCompany == null) {
      this.$router.push('login')
    }
  },
}
</script>

<style scoped>
</style>
