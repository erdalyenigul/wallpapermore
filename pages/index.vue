<template>
  <div class="container-fluid WMwrap">
    <div class="header">
      <h1 class="welcomeHead">WALLPAPER MORE! <br> Free wallpapers for everyone!</h1>
      <h2 class="welcomeMsg">
        A Huge collection of stunning HD wallpapers that you can find easily on this app, <br>change your device's background with thousands of choices and categories of HD Wallpepers. <br> Just search and download. Its totally free!
      </h2>
    </div>
    <div class="searchLine">
      <input type="text" placeholder="Search Photo.." v-model="searchKey" @keyup.enter="searchPhoto()">
      <a href="javascript:;" @click="searchPhoto()">Search</a>
    </div>
    <b-card-group columns v-if="photos">
      <b-card
        class="imgListWrap"
        v-for="item in photos" :key="item.id" 
        :img-src="item.src.large"
        overlay>
        <b-link target="_blank" :to="{ name: 'photo-id', params: { id: item.id} }"></b-link>
      </b-card>
    </b-card-group>
    <div class="loading" v-else><img src="@/static/loading.jpg" alt="Loading"></div>
    <div class="paginationWrap" v-if="paginationVisible">
      <a href="javascript:;" class="previousPage" @click="searchPhoto('previous')"><b-icon-chevron-left></b-icon-chevron-left></a>
      <span class="goToPage" @keyup.enter="searchPhoto()"><input type="text" placeholder="Page" v-model="page"></span>
      <a class="nextPage" @click="searchPhoto('next')"><b-icon-chevron-right></b-icon-chevron-right></a>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      photos: '',
      searchKey: '',
      page: 1,
      paginationVisible: false
    }
  },
  methods: {
    async searchPhoto(type) {
      if(type == 'next') {
        this.page++;
      } else if (type == 'previous') {
        this.page--;
      }
      await this.$axios.$get( process.env.baseURL + `/search?query=${this.searchKey}&page=${this.page}`, {
        headers: {
          Authorization: process.env.apiKey
        }
      }).then((response) => {
        this.photos = response.photos;
        this.paginationVisible = true;
      });
    },
    async getPhotos() {
      this.photos = '';
      await this.$axios.$get( process.env.baseURL + '/curated', {
        headers: {
          Authorization: process.env.apiKey
        }
      }).then((response) => {
        this.photos = response.photos;
      });
    }
  },
  async created() {
    this.getPhotos();
  }
}
</script>

<style lang="scss">
  .WMwrap{display:flex; flex-wrap:wrap; max-width:1280px;}
  .loading{display:flex; width:100%; align-items:center; justify-content:center;
    img{max-width:300px;}
  }
  .header{display:flex; flex-wrap:wrap; justify-content:center; width:100%; margin-bottom:20px; padding:20px 40px; background-color: #007bff; color: #fff; border-radius: 0 0 10px 10px;
    .welcomeHead{display:flex; justify-content: center; width:100%; margin-bottom: 20px; font-size:30px; line-height: 36px; text-align:center;}
		.welcomeMsg{display:flex; justify-content: center; width:100%; font-size:16px; line-height: 22px; text-align:center;}
  }
  .searchLine{width:100%; display:flex; margin-bottom:20px;
    input{border:4px solid #007bff; font-size:22px; text-align:center; border-radius:10px 0 0 10px; width:calc(100% - 120px); padding:10px; height:80px; outline:0; height:80px; border-right:0;}
    a{height:80px; width:120px; background-color:#007bff; color:#fff; display:flex; align-items:center; justify-content:center; border-radius:0 10px 10px 0;}
  }
  .imgListWrap{border-radius:10px; overflow:hidden;
    .card-img-overlay{padding:0;}
    a{display:flex; width:100%; height:100%; cursor:pointer;}
  }

  .paginationWrap{width:100%; display:flex; align-items:center; justify-content:center; padding:20px; margin: 0 auto; margin-bottom:100px;
    a{width:40px; height:40px; background-color:#007bff; color:#fff; display:flex; align-items:center; justify-content:center; font-size:16px; cursor:pointer;}
    .goToPage{width:80px; display:flex; align-items:center; justify-content:center; padding:0 5px;
      input{height:40px; width:100%; border:1px solid #007bff; padding:10px; font-size:16px; text-align:center; outline:0;}
    }
  }

  @media (max-width:767px) {
    .header{padding:20px; border-radius:0;}
    .header .welcomeHead{font-size:22px; line-height:26px; margin-bottom:0;}
    .header .welcomeMsg{display:none;}
    .imgListWrap{padding:10px;}
  }
</style>

