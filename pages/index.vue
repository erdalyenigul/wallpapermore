<template>
  <div id="main">
    <Header />
    <div class="container content">
      <div class="tags">
        <div class="tagHead">IF YOU WANT YOU CAN CHOOSE RELATED TAG</div>
        <a v-for="(item, index) in tags" :key="index"
        @click="searchTag(item)"
        href="javascript:;">
          #{{ item }}
        </a>
      </div>
      <div class="searchLine">
        <input type="text" placeholder="Search Photo.." v-model="searchKey" @keyup.enter="searchPhoto()">
        <a href="javascript:;" @click="searchPhoto()">Search</a>
      </div>
      <div class="photosHead" v-if="selectedPhotosText">Selected Photos <span>(at least one new photo per hour)</span></div>
      <div class="photosHead" v-else>Results for: {{ searchText }}</div>
      <b-card-group columns v-if="photos && !noResults && !loading">
        <b-card
          class="imgListWrap"
          v-for="item in photos" :key="item.id" 
          :img-src="item.src.large"
          overlay>
          <b-link target="_blank" :to="{ name: 'photo-id', params: { id: item.id} }"></b-link>
        </b-card>
      </b-card-group>
      <div v-if="loading" class="loading"><img src="@/static/loading.jpg" alt="Loading"></div>

      <div v-if="noResults" class="noResults">No Results about <strong> {{ searchText }} </strong></div>

      <div class="paginationWrap" v-if="totalPage != 1">
        <a v-if="pageNumber != 1" href="#main" class="previousPage" @click="getPhotos('previous')"><b-icon-chevron-left></b-icon-chevron-left></a>
        <span class="goToPage" @keyup.enter="getPhotos()"><input type="text" placeholder="Page" v-model="pageNumber"></span>
        <a v-if="pageNumber != totalPage " href="#main" class="nextPage" @click="getPhotos('next')"><b-icon-chevron-right></b-icon-chevron-right></a>
      </div>
      <div class="totalPage">Total Page: {{ totalPage }}</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedPhotosText: true,
      photos: false,
      searchKey: '',
      photoType: 'curated',
      searchText: '',
      totalPage: null,
      pageNumber: 1,
      noResults: false,
      loading: false,      
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
    searchPhoto() {
      this.pageNumber = 1;
      this.photoType = 'search';
      this.getPhotos();
    },
    async getPhotos(paginationType) {
      this.loading = true;
      if(paginationType == 'next') {
        this.pageNumber++;
      } else if (paginationType == 'previous') {
        this.pageNumber--;
      }
      
      if(this.photoType == 'curated') {
        this.selectedPhotosText = true;
        await this.$axios.$get( process.env.baseURL + `/curated?page=${this.pageNumber}`, {
          headers: {
            Authorization: process.env.apiKey
          }
        }).then((response) => {
          this.loading = false;
          if(response.total_results != 0) { 
            this.totalPage = Math.ceil(response.total_results / 15);
            this.photos = response.photos;
          };
        });
      } else if(this.photoType == 'search') {
        this.selectedPhotosText = false;
        this.searchText = this.searchKey;
        await this.$axios.$get( process.env.baseURL + `/search?query=${this.searchKey}&page=${this.pageNumber}`, {
          headers: {
            Authorization: process.env.apiKey
          }
        }).then((response) => {
          this.loading = false;
          if(response.total_results != 0) { 
            this.totalPage = Math.ceil(response.total_results / 15);
            this.photos = response.photos;
            this.noResults = false;
          } else {
            this.noResults = true;
          };
        });
      }
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
  body{font-family: 'Anek Tamil', sans-serif;}
  .content{display:flex; flex-wrap:wrap; max-width:1280px;}
  .loading{display:flex; width:100%; align-items:center; justify-content:center;
    img{max-width:300px;}
  }
  .searchLine{width:100%; display:flex; margin-bottom:20px;
    input{display:flex; align-items:center; border:1px solid #007bff; font-size:22px; border-radius:10px 0 0 10px; width:calc(100% - 120px); padding:10px 10px 10px 20px; outline:0; height:60px; border-right:0;}
    a{height:60px; width:120px; background-color:#007bff; color:#fff; display:flex; align-items:center; justify-content:center; border-radius:0 10px 10px 0;
      &:hover{text-decoration:none;}
    }
  }
  .tags{display:flex; justify-content:center;  align-items:center; flex-wrap: wrap; gap:10px; width:100%; padding:20px 0; border-bottom:1px solid #f1f1f1; margin-bottom:20px;
    .tagHead{display:flex; width:100%; margin-bottom:10px; font-size:18px; justify-content:center;}
    a{padding:5px 20px; color:#fff; color:#333; flex:1 1 auto; text-align:center; border:1px solid #f1f1f1; border-radius:6px;
      &:hover{text-decoration:none; background-color:#f1f1f1;}
    }
  }
  .photosHead{display:flex; align-items:center; width:100%; margin-bottom:10px; font-size:16px; font-weight:600;
    span{font-style:italic; font-size:11px; margin-left:5px; font-weight:400;}
  }
  .imgListWrap{border-radius:10px; overflow:hidden;
    .card-img-overlay{padding:0;}
    a{display:flex; width:100%; height:100%; cursor:pointer;}
  }
  .noResults{display:flex; width:100%; font-size:22px; justify-content:center; padding:20px 0;
    strong{padding:0 5px;}
  }
  .paginationWrap{width:100%; display:flex; align-items:center; justify-content:center; padding:20px; margin: 0 auto; 
    a{width:40px; height:40px; background-color:#007bff; color:#fff; display:flex; align-items:center; justify-content:center; font-size:16px; cursor:pointer;}
    .goToPage{width:60px; height:40px; display:flex; align-items:center; justify-content:center; padding:0 5px; border:1px solid #007bff;
      input{height:38px; width:100%; border:0; font-size:16px; text-align:center; outline:0;}
    }
  }
  .totalPage{width:100%; display:flex; align-items:center; justify-content:center; margin-bottom:100px;}

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

