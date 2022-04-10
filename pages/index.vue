<template>
  <div class="container WMwrap">
    <div class="header">
      <h1 class="welcomeHead">WALLPAPER MORE! <br> Free wallpapers for everyone!</h1>
      <h2 class="welcomeMsg">
        A Huge collection of stunning HD wallpapers that you can find easily on this app, <br>change your device's background with thousands of choices and categories of HD Wallpepers. <br> Just search and download. Its totally free!
      </h2>
    </div>
    <div class="searchLine">
      <input type="text" v-model="searchKey" @keyup.enter="search()">
    </div>
    <b-card-group columns>
      <b-card 
        v-for="item in selectedPhotos" :key="item.id" 
        :img-src="item.src.large" 
        img-alt="Image" 
        overlay
        @click="photoDetail(item)">
      </b-card>
    </b-card-group>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedPhotos: '',
      searchKey: ''
    }
  },
  methods: {
    async search() {
      if(this.searchKey.length > 3) {
        await this.$axios.$get( process.env.baseURL + `/search?query=${this.searchKey}`, {
          headers: {
            Authorization: process.env.apiKey
        }
        }).then((response) => {
          this.selectedPhotos = response.photos;
        });
      }
    },
    photoDetail(item) {
      console.log(item)
      this.$router.push({ name: 'photo-id', params: { id: item.id} })
    },
    async getPhotos() {
      await this.$axios.$get( process.env.baseURL + '/curated', {
        headers: {
          Authorization: process.env.apiKey
        }
      }).then((response) => {
        this.selectedPhotos = response.photos;
      });
    }
  },
  async created() {
    this.getPhotos();
  }
}
</script>

<style lang="scss">
  .WMwrap{display:flex; flex-wrap:wrap;}
  .header{display:flex; flex-wrap:wrap; justify-content:center; width:100%; margin-bottom:20px; padding:40px; background-color: #272727; color: #fff; border-radius: 0 0 20px 20px;
    .welcomeHead{display:flex; justify-content: center; width:100%; margin-bottom: 20px; font-size:50px; line-height: 60px; text-align:center;}
		.welcomeMsg{display:flex; justify-content: center; width:100%; margin-bottom: 40px; font-size:16px; line-height: 22px; text-align:center;}
  }
  .searchLine{width:100%; display:flex; margin-bottom:20px;
    input{border:1px solid #ddd; border-radius:10px; width:100%; padding:10px;}
  }
</style>

