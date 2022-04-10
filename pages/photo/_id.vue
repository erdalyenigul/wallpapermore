<template>
  <div class="container WMwrap">
    <div class="img" v-if="photoDetail">
      <img :src="photoDetail.src.large2x" alt="">
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      photoId: '',
      photoDetail: ''
    }
  },
  methods: {
    getPhotoDetail() {
      this.$axios.$get( process.env.baseURL + `/photos/${this.photoId}`, {
        headers: {
          Authorization: process.env.apiKey
        }
      }).then((response) => {
        console.log(response)
        this.photoDetail = response;
      });
    }
  },
  created() {
    this.photoId = this.$route.params.id
    this.getPhotoDetail();
  }
}
</script>

<style lang="scss">
  .WMwrap{display:flex; flex-wrap:wrap;}
  .img{width:100%; display:flex; justify-content:center;
    img{width:100%;}
  }
</style>

