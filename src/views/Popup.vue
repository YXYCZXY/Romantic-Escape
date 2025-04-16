<template>
    <div class="wrap">
      <el-image
        v-if="hasImg"
        class="preview-img"
        :src="url"
        :preview-src-list="srcList"
        fit="contain"
        lazy
      />
      <div v-else class="no-img">暂无图片信息</div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'Popup',
    props: ['objes'],
    data() {
      return {
        url: '',
        srcList: [],
      }
    },
    computed: {
      hasImg() {
        return this.objes && this.objes.img;
      },
    },
    mounted() {
      if (this.hasImg) {
        this.url = this.objes.img;
        this.srcList = [this.objes.img];
      }
    },
  }
  </script>
  
  <style scoped>
  .wrap {
    max-width: 300px;
    padding: 8px;
    border-radius: 8px;
    background: #fff;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  }
  
  .preview-img {
    width: 100%;
    height: auto;
    max-height: 200px;
    object-fit: contain;
    border-radius: 6px;
    display: block;
    margin: 0 auto;
  }
  
  .no-img {
    text-align: center;
    color: #999;
    font-size: 14px;
  }
  </style>
  