<template >
  <q-card class="custom-area" v-touch-repeat:0:100.left.right.38.40="handleRepeat" tabindex="0"
    @dblclick="mousemoveOrNone()" v-focus>
    <v-chart class="chart" :option="option" />
  </q-card>
</template>
  
<script>
import * as echarts from 'echarts/core';
import {
  ToolboxComponent,
  TooltipComponent,
  GridComponent,
  VisualMapComponent,
  LegendComponent,
  BrushComponent,
  DataZoomComponent
} from 'echarts/components';
import { CandlestickChart, LineChart, BarChart, ScatterChart } from 'echarts/charts';
import { UniversalTransition } from 'echarts/features';
import { CanvasRenderer } from 'echarts/renderers';

import VChart from 'vue-echarts';
import { defineComponent, reactive, onMounted, ref } from 'vue';
import { api } from 'boot/axios'

echarts.use([
  ToolboxComponent,
  TooltipComponent,
  GridComponent,
  VisualMapComponent,
  LegendComponent,
  BrushComponent,
  DataZoomComponent,
  CandlestickChart,
  LineChart,
  BarChart,
  ScatterChart,
  CanvasRenderer,
  UniversalTransition
]);
const focus = {
  mounted: (el) => el.focus()
}
export default defineComponent({
  name: 'StockStandardk',
  components: {
    VChart,
  },
  directives: {
    // 在模板中启用 v-focus
    focus
  },
  props: {
    mydata: {
      type: String
    },
  },
  setup(props) {
    // console.log(props.mydata, 'uu')
    let da_num = ref(0);
    onMounted(() => {
      api.get('/polls/5/stockStandardk/', {
        params: {
          inp: props.mydata
        },
      }).then(res => {
        let data = res.data
        let categoryData = data.categoryData
        let values = data.values
        option.xAxis[0].data = categoryData
        option.xAxis[1].data = categoryData
        option.series[0].data = values
        da_num.value = categoryData.length
        option.dataZoom[0].startValue = categoryData.length - 350
        option.dataZoom[0].endValue = categoryData.length
        option.series[1].data = calculateMA(5, values)
        option.series[2].data = calculateMA(10, values)
        option.series[3].data = calculateMA(20, values)
        option.series[4].data = calculateMA(30, values)
        // option.series[5].data = data.dat_yj_yg
        option.series[6].data = data.volumes
        // console.log(values);
      }).catch((err) => {
        console.log(err);
      });
    });
    function calculateMA(dayCount, data) {
      var result = [];
      // console.log(data)
      for (var i = 0, len = data.length; i < len; i++) {
        if (i < dayCount) {
          result.push('-');
          continue;
        }
        var sum = 0;
        for (var j = 0; j < dayCount; j++) {
          sum += data[i - j][1];
        }
        result.push(+(sum / dayCount).toFixed(3));
      }
      return result;
    };
    function mousemoveOrNone() {
      option.tooltip.axisPointer.type = (option.tooltip.axisPointer.type == 'none') ? 'cross' : 'none'
      option.tooltip.triggerOn = (option.tooltip.triggerOn == 'click') ? 'mousemove' : 'click'
    };
    const option = reactive({
      animation: false,
      legend: {
        bottom: 1,
        left: 'center',
      },
      tooltip: {
        axisPointer: {
          type: 'none',
        },
        triggerOn: 'click',
        showDelay: 500,
        confine: true,
        padding: 0,
        textStyle: {
          fontSize: 12,
          color: '#000'
        },
        formatter: function (param) {
          // console.log(param)'amount', 'amplitude', 'up_change', 'num_change', 'turnover'
          return [
            '' + param.name + '<hr size=1 style="margin: 3px 0">',
            '开盘:' + param.data[1] + '<br/>',
            '收盘:' + param.data[2] + '<br/>',
            '最低:' + param.data[3] + '<br/>',
            '最高:' + param.data[4] + '<br/>',
            '成交量:' + (param.data[5] / 10000000).toFixed(1) + 'kw<br/>',
            '成交额:' + (param.data[6] / 10000000).toFixed(1) + 'kw<br/>',
            '换手率:' + param.data[7] + '<br/>',
            '涨跌幅:' + param.data[8] + '<br/>',
            '市盈率:' + param.data[9] + '<br/>',
            '市净率:' + param.data[10] + '<br/>',
            '市销率:' + param.data[11] + '<br/>',
          ].join('');
        }
      },
      axisPointer: {
        snap: true,
        // triggerOn: 'none',
        link: [{ xAxisIndex: 'all' }]
      },
      visualMap: {
        show: false,
        seriesIndex: 6,
        dimension: 2,
        pieces: [
          {
            value: 1,
            color: '#ec0000'
          },
          {
            value: -1,
            color: '#00da3c'
          }
        ]
      },
      grid: [
        {
          left: '8%',
          right: '1%',
          height: '65%'
        },
        {
          left: '8%',
          right: '1%',
          top: '80%',
          height: '10%'
        },
      ],
      xAxis: [
        {
          type: 'category',
          data: [],
          // data: data.dat.categoryData,
          boundaryGap: true,
          axisLine: { onZero: false },
          splitLine: { show: false },
          axisTick: { show: false },
          axisLabel: { show: false },
          min: 'dataMin',
          max: 'dataMax',
          axisPointer: {
            z: 100
          }
        },
        {
          type: 'category',
          gridIndex: 1,
          data: [],
          // data: data.dat.categoryData,
          boundaryGap: true,
          axisLine: { onZero: false },
          axisTick: { show: false },
          splitLine: { show: false },
          axisLabel: { show: false },
          min: 'dataMin',
          max: 'dataMax',
          axisPointer: {
            label: { show: false },
          }
        },
      ],
      yAxis: [
        {
          scale: true,
          splitArea: {
            show: false
          },
          splitLine: {                 //坐标轴在 grid 区域中的分隔线。
            show: true,              //是否显示分隔线。默认数值轴显示，类目轴不显示。
            interval: 'auto',        //坐标轴分隔线的显示间隔，在类目轴中有效。默认会采用标签不重叠的策略间隔显示标签。可以设置成 0 强制显示所有标签。如果设置为 1，表示『隔一个标签显示一个标签』，可以用数值表示间隔的数据，也可以通过回调函数控制。回调函数格式如下：
            lineStyle: {
              color: '#333',
              type: 'solid', //坐标轴线线的类型，solid，dashed，dotted
              width: 0.5,    //坐标轴线线宽
              opacity: 1,    //图形透明度。支持从 0 到 1 的数字，为 0 时不绘制该图形
            },
          },
        },
        {
          // name: 'hk',
          // type: 'value',
          scale: true,
          gridIndex: 1,
          splitNumber: 3,
          axisLabel: {
            show: true, formatter: function (param) {
              return (param / 10000) + 'w'
            }
          },
          axisLine: { show: false },
          splitLine: {
            show: true,
            interval: 'auto',
            lineStyle: {
              color: '#333',
              type: 'solid', //坐标轴线线的类型，solid，dashed，dotted
              width: 0.5,    //坐标轴线线宽
              opacity: 1,    //图形透明度。支持从 0 到 1 的数字，为 0 时不绘制该图形
            },
          }
        },
      ],
      dataZoom: [
        {
          type: 'inside',
          xAxisIndex: [0, 1],
          zoomOnMouseWheel: 'alt',
          startValue: 0,
          endValue: 150
        },
      ],
      series: [
        {
          name: 'Dow',
          type: 'candlestick',
          data: [
            [20, 34, 10, 38],
            [40, 35, 30, 50],
            [31, 38, 33, 44]
          ],
          // data: data.dat.values,
          itemStyle: {
            color0: '#00da3c',
            color: '#ec0000',
            borderColor0: '#0CF49B',
            borderColor: '#FD1050'
          },
        },
        {
          name: 'MA5',
          type: 'line',
          data: [],
          // data: calculateMA(5, data.dat.values),
          smooth: true,
          showSymbol: false,
          lineStyle: {
            width: 0.5
          },
          tooltip: {
            show: false,
          }
        },
        {
          name: 'MA10',
          type: 'line',
          data: [],
          // data: calculateMA(10, data.dat.values),
          smooth: true,
          showSymbol: false,
          lineStyle: {
            width: 0.5
          },
          tooltip: {
            show: false,
          }
        },
        {
          name: 'MA20',
          type: 'line',
          data: [],
          // data: calculateMA(20, data.dat.values),
          smooth: true,
          showSymbol: false,
          lineStyle: {
            width: 0.5
          },
          tooltip: {
            show: false,
          }
        },
        {
          name: 'MA30',
          type: 'line',
          data: [],
          // data: calculateMA(30, data.dat.values),
          smooth: true,
          showSymbol: false,
          lineStyle: {
            width: 0.5
          },
          tooltip: {
            show: false,
          },
        },
        {
          // name: 'gg',
          type: 'scatter',
          symbolSize: 12,
          data: [],
          // data: data.dat_yj_yg,
          tooltip: {
            position: ['0%', '0%'],
            formatter: function (param) {
              param = param.data;
              return [
                '<div style="word-break: break-all;word-wrap: break-word;white-space: normal;">'
                + param[2] + ',' + param[3] + ',' + param[4] + ',' + param[5] + '<br/>' + param[6] + '</div>'
              ].join('');
            }
          },
        },
        {
          name: 'Volume',
          type: 'bar',
          xAxisIndex: 1,
          yAxisIndex: 1,
          stack: 'x',
          data: [],
          // data: data.dat.volumes,
          tooltip: {
            show: false,
          }
        },
      ]
    });
    function handleRepeat({ evt }) {
      // console.log(evt)
      switch (evt.keyCode) {
        case 38: // ArrowUp 开始值小于数据长度还能加
          if (option.dataZoom[0].endValue != da_num.value) {
            option.dataZoom[0].endValue = da_num.value
          }
          if (option.dataZoom[0].startValue <= (da_num.value - 50)) {
            option.dataZoom[0].startValue += 200
          }
          // console.log(option.dataZoom[0].startValue, option.dataZoom[0].endValue)
          break
        case 40: // ArrowDown 开始值大于0还能减
          if (option.dataZoom[0].endValue != da_num.value) {
            option.dataZoom[0].endValue = da_num.value
          }
          if (option.dataZoom[0].startValue >= 50) {
            option.dataZoom[0].startValue -= 200
          }
          break
        case 37: // left 开始值大于0还能减
          // console.log(option.dataZoom[0].startValue, option.dataZoom[0].endValue)
          if (option.dataZoom[0].startValue >= 0) {
            option.dataZoom[0].startValue -= 20
          }
          if (option.dataZoom[0].endValue >= 0) {
            option.dataZoom[0].endValue -= 20
          }
          break
        case 39: // right 开始值小于数据长度还能加
          // console.log(option.dataZoom[0].startValue, option.dataZoom[0].endValue)
          if (option.dataZoom[0].startValue <= (da_num.value - 50)) {
            option.dataZoom[0].startValue += 20
          }
          if (option.dataZoom[0].endValue <= (da_num.value)) {
            option.dataZoom[0].endValue += 20
          }
          break
        case 36: // Home
          break
        case 35: // End
          break
        case 33: // PageUp
          break
        case 34: // PageDown
          break


      }
    }

    return { option, mousemoveOrNone, handleRepeat }
  },
});
</script>

<style scoped>
.chart {
  height: 82vh;
  width: 98vw;
}
</style>
<style lang="sass" scoped>
.custom-area
  &:focus
    outline: 0px 
    outline-offset: 0px
</style>
  