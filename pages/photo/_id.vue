<template>
  <div class="container-fluid WMwrap">
    <div class="detailHead" v-if="photoDetail">
      <div class="headBtns">
        <a class="backBtn" href="javascript:;" @click="backToMain()"> 
          <b-icon-chevron-left></b-icon-chevron-left>
        </a>
        <a class="downloadBtn" @click="downloadImg()" :title="photoDetail.alt">Download Wallpaper</a>
      </div>
      <div class="imgInfo" v-if="photoDetail.alt">
        <div class="description">{{ photoDetail.alt }} </div>
      </div>
    </div>
    <div class="imgWrap" v-if="photoDetail">
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
    backToMain() {
      this.$router.push('/')
    },
    async downloadImg(){
      const fileName = this.photoDetail.src.original;
      try {
        const response = await fetch(fileName)
        const blob = await response.blob();
        const url = await URL.createObjectURL(blob);
        const a = document.createElement("a");
        a.href = url;
        a.download = 'photo' + this.photoDetail.id + 'jpg';
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
      } catch(err) {
        console.log({ err })
      }
    },
    getPhotoDetail() {
      this.$axios.$get( process.env.baseURL + `/photos/${this.photoId}`, {
        headers: {
          Authorization: process.env.apiKey
        }
      }).then((response) => {
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

<style lang="scss" scoped>
  .WMwrap{display:flex; align-items:flex-start; flex-wrap:wrap; padding:0; min-height:100vh;}
  .imgWrap{width:100%; max-width:800px; margin:0 auto; padding:40px; display:flex; justify-content:center;
    img{width:100%;}
  }
  .detailHead{width:100%; height:90px; align-items:center; display:flex; justify-content:center; align-items:center; background-color:#fff; border-bottom:1px solid #ddd;}
  .imgInfo{display:flex; width:calc(100% - 400px); flex-wrap:wrap; padding:20px; flex-direction:column; border-left:1px solid #ddd;
    .description{font-size:18px; font-weight:600;}
    .photographer{font-size:14px; font-style:italic;}
  }
  .headBtns{display:flex; width:400px; padding:10px 40px;
    .backBtn{cursor:pointer; padding:3px 10px; display: flex; align-items: center; justify-content: center; color:#fff; background-color:#222; margin-right:10px;
      svg{font-size:22px; font-weight:600;}
    }
    .downloadBtn{cursor:pointer; width:100%; display:flex; align-items:center; justify-content:center; background-color:#007bff; color:#fff!important; padding:10px 20px;}
  }

  @media (max-width:767px) {
    .detailHead{flex-direction:column; height:auto;
      .headBtns{width:100%; padding:30px 40px;}
      .imgInfo{width:100%; padding:0 40px 30px;}
    }
  }
</style>

