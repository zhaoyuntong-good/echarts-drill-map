
<template>
  <div :id="domId" class="echarts-drill-map"></div>
</template>

<script>
  import echarts from 'echarts/lib/echarts';
  import chinaJson from './mapjs/china.js'
  import mapping from './mapjs/mapping.js'
  export default {
    name: 'echarts-drill-map',
    components:{},
    props: {
      mapConfig: {
        type: Object,
        required: true
      }
    },
    data() {
      return {
      	domId: null,
        myChart: null,
        areaName: null,
        params: null,
        stackArr: [],
      };
    },
    watch: {
      myChart: function(){
        // 绑定下钻事件
        const eventType = this.mapConfig.drillType || 'click';
        this.myChart.on(eventType, params => {
          this.areaName = params.name;
          this.params = params;
          this.registerMap(params.name, true);
        });
        if (eventType === 'dblclick') {
          this.myChart.on('click', params => {
            this.areaName = params.name;
            this.params = params;
          });
        }
      },
      areaName: function(){
        this.$emit('mapClick',this.params)
      }
    },
    created(){
      this.domId = this.randomStr(6);
    },
    mounted(){
      // 这里进行数据类型判断
      if( typeof this.mapConfig.name !== 'string'){
        throw "the mapConfig.name must be string";
        return;
      }
      if( typeof this.mapConfig.drillType !== 'string'){
        throw "the mapConfig.drillType must be string";
        return;
      }
      this.myChart = echarts.init(document.getElementById(this.domId));
      this.mapConfig.resize && window.addEventListener('resize', this.mapResize);
      // 执行地图注册
      this.registerMap(this.mapConfig.name, true);
    },
    methods: {
      // 随机字符串
      randomStr(len){
        let $chars = 'ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz';
        let maxPos = $chars.length;
        let pwd = '';
        for (let i = 0; i < len; i++) {
          pwd += $chars.charAt(Math.floor(Math.random() * maxPos));
        }
        return pwd;
      },
      // 地图注册
      registerMap(mapName, drillDown){
        let option = JSON.parse(JSON.stringify(this.mapConfig.option));
        option.geo.map = mapName;
        option.geo.show = true;
        option.geo.type = 'map';
        let series0 = [
          {
            type: 'map',
            map: mapName,
            geoIndex: 0,
          }
        ];
        let series1 = option.series;
        option.series = [
          ...series0,
          ...series1
        ];
        if (mapName === 'china') {
          import('./mapjs/china.js').then( res => {
            echarts.registerMap(mapName, res.default);
            this.myChart.setOption(option);
          })
        } else {
          if (mapping[mapName]) {
            let mapGeoJson = mapping[mapName];
            import(`./mapjs/other/${mapGeoJson}.js`).then( res => {
              echarts.registerMap(mapName, res.default);
              this.myChart.setOption(option);
              drillDown && this.stackArr.push(mapName);
            })
          }
          
        }
      },
      // 删除栈顶
      truncate(arr) {
        return arr.filter((v, i, ar) => {
          return i !== ar.length - 1
        })
      },
      // 地图返回
      back(){
        if (this.stackArr.length === 0 || this.stackArr.length === 1) {
          this.myChart.clear();
          this.registerMap(this.mapConfig.name);
          // 清除栈
          this.stackArr = [];
        } else {
          // 返回上级地图
          this.registerMap(this.stackArr[this.stackArr.length - 2], false);
          // 清除栈顶
          this.stackArr = this.truncate(this.stackArr);
        }
      },
      // 地图响应式
      mapResize(){
        this.myChart && this.myChart.resize();
      }
    },
    beforeDestroy(){
      this.mapConfig.resize && window.removeEventListener('resize', this.mapResize);
      // 清除实例
      if (this.myChart) {
        this.myChart.clear();
        this.myChart.dispose();
        this.myChart = null;
      }
    }
  };
</script>

<style scoped>
  .echarts-drill-map {
    width: 100%;
    height: 100%;
  }
</style>
