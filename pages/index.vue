<template>
  <div id="main">
    <div class="header">
      <h1 class="welcomeHead">WALLPAPER MORE! <br> Free wallpapers for everyone!</h1>
      <h2 class="welcomeMsg">
        A Huge collection of stunning HD wallpapers that you can find easily on this app, <br>change your device's background with thousands of choices and categories of HD Wallpepers. <br> Just search and download. Its totally free!
      </h2>
    </div>
    <div class="container content">
      <div class="searchLine">
        <input type="text" placeholder="Search Photo.." v-model="searchKey" @keyup.enter="searchPhoto()">
        <a href="javascript:;" @click="searchPhoto()">Search</a>
      </div>
      <div class="tags">
        <a v-for="(item, index) in tags" :key="index" 
        :style="{ backgroundColor: randomColor() }" 
        @click="searchTag(item)"
        href="javascript:;">
          #{{ item }}
        </a>
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
      <div class="paginationWrap" v-if="selectedPhotosPagination">
        <a href="javascript:;" class="previousPage" @click="getPhotos('previous')"><b-icon-chevron-left></b-icon-chevron-left></a>
        <span class="goToPage" @keyup.enter="getPhotos()"><input type="text" placeholder="Page" v-model="selectedPhotosPageNumber"></span>
        <a href="javascript:;" class="nextPage" @click="getPhotos('next')"><b-icon-chevron-right></b-icon-chevron-right></a>
      </div>
      <div class="paginationWrap" v-if="searchPagination">
        <a href="javascript:;" class="previousPage" @click="searchPhoto('previous')"><b-icon-chevron-left></b-icon-chevron-left></a>
        <span class="goToPage" @keyup.enter="searchPhoto()"><input type="text" placeholder="Page" v-model="searchPageNumber"></span>
        <a href="javascript:;" class="nextPage" @click="searchPhoto('next')"><b-icon-chevron-right></b-icon-chevron-right></a>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      photos: '',
      searchKey: '',
      selectedPhotosPageNumber: 1,
      searchPageNumber: 1,
      selectedPhotosPagination: true,
      searchPagination: false,
      tags: [
        'sky',
        'mountain',
        'road',
        'cloud',
        'beach',
        'flower',
        'macro',
        'rain',
        'black'
      ]
    }
  },
  methods: {
    searchTag(tag) {
      this.searchKey = tag;
      this.searchPhoto();
    },
    async searchPhoto(type) {
      this.selectedPhotosPagination = false;
      this.searchPagination = true;
      if(type == 'next') {
        this.searchPageNumber++;
      } else if (type == 'previous') {
        this.searchPageNumber--;
      }
      this.photos = '';
      await this.$axios.$get( process.env.baseURL + `/search?query=${this.searchKey}&page=${this.searchPageNumber}`, {
        headers: {
          Authorization: process.env.apiKey
        }
      }).then((response) => {
        this.photos = response.photos;
        this.paginationVisible = true;
      });
    },
    async getPhotos(type) {
      this.searchPagination = false;
      this.selectedPhotosPagination = true;
      if(type == 'next') {
        this.selectedPhotosPageNumber++;
      } else if (type == 'previous') {
        this.selectedPhotosPageNumber--;
      }
      this.photos = '';
      await this.$axios.$get( process.env.baseURL + `/curated?page=${this.selectedPhotosPageNumber}`, {
        headers: {
          Authorization: process.env.apiKey
        }
      }).then((response) => {
        this.photos = response.photos;
      });
    },
    randomColor() {
      let maxVal = 0xFFFFFF;
      let randomNumber = Math.random() * maxVal; 
      randomNumber = Math.floor(randomNumber);
      randomNumber = randomNumber.toString(16);
      let randColor = randomNumber.padStart(6, 0);   
      return `#${randColor.toUpperCase()}`
    }
  },
  async created() {
    this.getPhotos();
  }
}
</script>

<style lang="scss">
  @import url('https://fonts.googleapis.com/css2?family=Anek+Tamil:wght@400;500;600&display=swap');
  .content{display:flex; flex-wrap:wrap; max-width:1280px;}
  .loading{display:flex; width:100%; align-items:center; justify-content:center;
    img{max-width:300px;}
  }
  .header{display:flex; flex-wrap:wrap; justify-content:center; width:100%; margin-bottom:20px; padding:20px 40px; background-color: #007bff; color: #fff;
    .welcomeHead{display:flex; justify-content: center; width:100%; margin-bottom: 20px; font-size:30px; line-height: 36px; text-align:center; font-family: 'Anek Tamil', sans-serif;}
		.welcomeMsg{display:flex; justify-content: center; width:100%; font-size:16px; line-height: 22px; text-align:center;}
  }
  .searchLine{width:100%; display:flex; margin-bottom:20px;
    input{display:flex; align-items:center; border:1px solid #007bff; font-size:22px; border-radius:10px 0 0 10px; width:calc(100% - 120px); padding:10px 10px 10px 20px; outline:0; height:60px; border-right:0;}
    a{height:60px; width:120px; background-color:#007bff; color:#fff; display:flex; align-items:center; justify-content:center; border-radius:0 10px 10px 0;
      &:hover{text-decoration:none;}
    }
  }
  .tags{display:flex; justify-content:center;  align-items:center; flex-wrap: wrap; gap:10px 5px; width:100%; padding:20px;
    a{padding:5px 20px; color:#fff; border-radius:6px; text-shadow: 1px 1px 5px #999; flex:1 1 auto; text-align:center;
      &:hover{text-decoration:none;}
    }
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
    .tags{padding: 20px 0;}
    .searchLine input{font-size:16px; height:50px; border-radius:5px 0 0 5px; width:calc(100% - 80px);}
    .searchLine a{font-size:16px; height:50px; border-radius:0 5px 5px 0; width:80px;}
  }
</style>

