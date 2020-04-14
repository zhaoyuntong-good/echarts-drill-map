
## install the plugin
```
npm install echarts-drill-map -g
```

### import the plugin in template
```
import echartsDrillMap from 'echarts-drill-map'
```

### use in template
```
<echarts-drill-map ref="echartsDrillMap" :mapConfig="mapConfig" @mapClick="yourSelfeClick"></echarts-drill-map>
```

### the mapConfig in data
```
data() {
  return {
    mapConfig: {
      name: 'china', // the map name,if you want to show a province,change this key like '河北'
      drillType: 'click', // the drill down type
      resize: true, // open window resize
      option: { // your option
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
        series: [] // other series
      }
    }
  };
},
```

### the method in methods
```
methods: {
  mapClick(val){
    // do something
  },
  mapBack(){
    this.$refs.echartsDrillMap.back();
  }
}
```
### the method of return back in map
```
methods: {
  mapBack(){
    this.$refs.echartsDrillMap.back();
  }
}
```
### the method of return back in map
```
Current version supports drilling in Beijing, Hebei and Shanxi
```