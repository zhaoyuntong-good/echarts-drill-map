<template>
  <div id="app">
    <echarts-drill-map ref="echartsDrillMap" :mapConfig="mapConfig" @mapClick="mapClick"></echarts-drill-map>
    <div class="back" @click="mapBack()">返回</div>
  </div>
</template>

<script>
  import "echarts/lib/chart/map";
  import "echarts/lib/chart/effectScatter";
  import "echarts/lib/component/geo";
  import echartsDrillMap from './components/plugins/echarts-drill-map'
  export default {
    name: 'App',
    components: {
      echartsDrillMap
    },
    data() {
      return {
        mapConfig: {
          name: 'china',
          drillType: 'click',
          resize: true,
          option: {
            geo: {
              roam: true,
              itemStyle: {
                areaColor: 'rgba(0,0,0,0)',
                borderColor: '#6cb2ed',
                borderWidth:1.1
              },
              emphasis: {
                itemStyle: {
                  areaColor: 'rgba(34,105,222,0.5)',
                  borderColor: '#6cb2ed'
                }
              }
            },
            series: []
          }
        }
      };
    },
    created(){
    },
    methods: {
      mapClick(val){
        console.log(val)
      },
      mapBack(){
        this.$refs.echartsDrillMap.back();
      }
    }
  }
</script>

<style>
* {
  margin: 0;
  padding: 0;
}
#app {
  width: 100vw;
  height: 100vh;
}
.back{
    position: fixed;
    top: 20px;
    right: 20px;
    cursor: pointer;
  }
</style>
