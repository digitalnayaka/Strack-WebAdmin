<template>
  <div>
    ini halaman depan
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
    
  },
}
</script>

<style scoped>
</style>
