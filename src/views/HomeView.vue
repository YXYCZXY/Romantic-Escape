<template>
  <div id="map">
    <div class="select-radio">
      <el-radio v-model="radio" label="1">大阪</el-radio>
      <el-radio v-model="radio" label="2">京都</el-radio>
      <el-radio v-model="radio" label="3">奈良</el-radio>
    </div>
    <img src="../assets/life.png" alt="" class="img-life">
  </div>
</template>

<script>
import maplibregl from 'maplibre-gl';
import 'maplibre-gl/dist/maplibre-gl.css';
import MapboxLanguage from '@mapbox/mapbox-gl-language';
import { jd } from './geojosn';
export default {
  name: '',
  mixins: [],
  components: {

  },
  props: {},
  data() {
    return {
      map: null,
      radio: '1'
    }
  },
  computed: {},
  mounted() {
    this.init()
  },
  methods: {
    init() {

      const map = new maplibregl.Map({
        container: 'map', // container id
        style: 'https://api.maptiler.com/maps/jp-mierune-streets/style.json?key=EyCzsrRs2mSzz370MR3N', // style URL
        center: [135.45814476620478, 34.685666338145985], // starting position [lng, lat]
        zoom: 11 // starting zoom
      });


      map.addControl(new MapboxLanguage({
        defaultLanguage: 'zh-Hans'
      }));
      this.map = map
      map.on('load', async () => {
        let url = require('../assets/point.png')
        let image = await map.loadImage(url);
        map.addImage('point-jp', image.data);

        let url2 = require('../assets/eat.png')
        let image2 = await map.loadImage(url2);
        map.addImage('eat-jp', image2.data);



        this.addLin(jd.line)
        this.addPoint(jd.point)
      })

    },
    addLin(line) {
      let map = this.map

      map.addSource('sightseeing-points', {
        type: 'geojson',
        data: line
      });

      map.addLayer({
        id: 'sightseeing-line',
        type: 'line',
        source: 'sightseeing-points',
        filter: ['==', '$type', 'LineString'],
        layout: {
          'line-join': 'round',  // 转角平滑
          'line-cap': 'round'    // 端点圆润
        },
        paint: {
          'line-color': '#1E88E5', // 鲜明的蓝色
          'line-width': 5,
          'line-opacity': 0.9,
          // 如需虚线风格，可以启用下面属性
          // 'line-dasharray': [2, 2]
        }
      });

      /* 
        优化设计：景点标注（点）
        - 显示文字信息：“顺序. 名称”；
        - 使用定制图标（这里用 'point-icon' 作为示例，需提前加载）；
        - 图标大小和偏移量经过优化，与地图整体设计风格搭配；
        - 文字使用较稳重的颜色，并添加描边以增强对比度
      */
      // 如果需要自定义图标，请在加载时先调用 map.loadImage() 加载你的图标：
      // map.loadImage('path/to/your/icon.png', (error, image) => { ... });
      // 或者使用 Maptiler 已有的图标资源

      map.addLayer({
        id: 'sightseeing-points-layer',
        type: 'symbol',
        source: 'sightseeing-points',
        filter: ['==', '$type', 'Point'],
        layout: {
          'icon-image': 'point-jp',             // 确保图标存在
          'icon-size': 1.0,
          'icon-allow-overlap': true,           // 图标允许重叠
          'icon-ignore-placement': true,        // 忽略图标位置冲突

          'text-field': ['concat', ['get', 'order'], '. ', ['get', 'name']],
          'text-font': ['Open Sans Bold', 'Arial Unicode MS Bold'],
          'text-size': 12,
          'text-offset': [0, 1.5],
          'text-anchor': 'top',

          'text-allow-overlap': true,           // 文字允许重叠
          'text-ignore-placement': true         // 忽略文字遮挡规则
        },
        paint: {
          'text-color': '#222222',              // 深色字体
          'text-halo-color': '#FFFFFF',         // 白描边增强对比
          'text-halo-width': 1.5
        }
      });


    },
    addPoint(line) {
      let map = this.map
      map.addSource('eat-points', {
        type: 'geojson',
        data: line
      });

      map.addLayer({
        id: 'eat-points-layer',
        type: 'symbol',
        source: 'eat-points',

        layout: {
          'icon-image': 'eat-jp',             // 确保图标存在
          'icon-size': 1.0,
          // 'icon-allow-overlap': true,           // 图标允许重叠
          // 'icon-ignore-placement': true,        // 忽略图标位置冲突

          'text-field': ['concat', ['get', 'name'], '. ', ['get', 'time']],
          'text-font': ['Open Sans Bold', 'Arial Unicode MS Bold'],
          'text-size': 12,
          'text-offset': [0, 1.5],
          'text-anchor': 'top',

          'text-allow-overlap': true,           // 文字允许重叠
          'text-ignore-placement': true         // 忽略文字遮挡规则
        },
        paint: {
          'text-color': '#222222',              // 深色字体
          'text-halo-color': '#FFFFFF',         // 白描边增强对比
          'text-halo-width': 1.5
        }
      });
      map.on('click', 'eat-points-layer', (e) => {
        debugger
        if (e.features && e.features.length > 0) {
          const properties = e.features[0].properties;
          const name = properties.name;
          const url = properties.url;
          // 复制到剪贴板
          navigator.clipboard.writeText(name).then(() => {
            this.$message({
              message: '已复制',
              type: 'success'
            });
          }).catch(err => {
            console.error('复制失败：', err);
          });
          // 如果有 URL，就打开新窗口跳转
          if (url) {
            window.open(url, '_blank');
          }
        }
      });
    }
  },
  watch: {
    radio(val) {
      let map = this.map
      if (val === '1') {
        map.flyTo({ center: [135.45814476620478, 34.685666338145985], zoom: 11 });
      } else if (val === '2') {
        map.flyTo({ center: [135.75687250555325, 34.98237573473588], zoom: 11 });
      } else {
        map.flyTo({ center: [135.82428483532448, 34.685564126994336], zoom: 11 });
      }
    }
  }
}
</script>

<style scoped>
.img-life {
  position: absolute;
  top: -60px;
  right: -100px;
  z-index: 1111111;
  transform: scale(0.3)
}

.select-radio {
  position: absolute;
  top: 10px;
  left: 10px;
  z-index: 1111111;
}

#map {
  width: 100%;
  height: 100%
}
</style>
