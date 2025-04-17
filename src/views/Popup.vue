<template>
  <div class="wrap">
    <div v-if="hasImg">
      <el-image class="preview-img" :src="url" :preview-src-list="srcList" fit="contain" />
    </div>
    <div v-else>
      <p>暂无图片信息</p>
    </div>
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
  width: 300px;
  height: 200px;
  padding: 8px;
  border-radius: 8px;
  background: #fff;
  position: relative;
}

.preview-img {
  position: absolute;
  top: 8px;
  left: 8px;
  right: 8px;
  bottom: 8px;
  object-fit: contain;
  width: calc(100% - 16px);
  height: calc(100% - 16px);
  border-radius: 6px;
}
</style>
