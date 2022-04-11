<template>
  <div class="container WMwrap">
    <div class="header">
      <h1 class="welcomeHead">WALLPAPER MORE! <br> Free wallpapers for everyone!</h1>
      <h2 class="welcomeMsg">
        A Huge collection of stunning HD wallpapers that you can find easily on this app, <br>change your device's background with thousands of choices and categories of HD Wallpepers. <br> Just search and download. Its totally free!
      </h2>
    </div>
    <div class="searchLine">
      <input type="text" placeholder="Search Photo.." v-model="searchKey" @keyup.enter="search()">
    </div>
    <b-card-group columns>
      <b-card
        class="imgListWrap"
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
      
      if(this.searchKey.length > 2) {
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
  .header{display:flex; flex-wrap:wrap; justify-content:center; width:100%; margin-bottom:20px; padding:20px 40px; background-color: #3e183b; color: #fff; border-radius: 0 0 10px 10px;
    .welcomeHead{display:flex; justify-content: center; width:100%; margin-bottom: 20px; font-size:30px; line-height: 36px; text-align:center;}
		.welcomeMsg{display:flex; justify-content: center; width:100%; font-size:16px; line-height: 22px; text-align:center;}
  }
  .searchLine{width:100%; display:flex; margin-bottom:20px;
    input{border:4px solid #ccc; font-size:22px; text-align:center; border-radius:10px; width:100%; padding:10px; height:80px; outline:0;}
  }
  .imgListWrap{border-radius:10px; overflow:hidden; box-shadow: rgba(100, 100, 111, 0.2) 0px 7px 29px 0px; cursor:pointer;}

  @media (max-width:767px) {
    .header{padding:20px; border-radius:0;}
    .header .welcomeHead{font-size:22px; line-height:26px; margin-bottom:0;}
    .header .welcomeMsg{display:none;}
    .searchLine input{border-radius:0;}
    .imgListWrap{padding:10px;}
  }
</style>

