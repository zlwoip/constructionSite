<template>
  <!-- 表单渲染 -->
  <el-dialog
    append-to-body
    :close-on-click-modal="false"
    :before-close="cancelView"
    :visible="visible"
    :title="title"
    top="25vh"
    width="680px"
  >
    <div style="padding-left: 20px">
      <span class="label">检索时间范围:</span>
      <el-date-picker
        v-model="timeList"
        type="datetimerange"
        :picker-options="pickerOptions"
        range-separator="至"
        start-placeholder="开始日期"
        end-placeholder="结束日期"
        size="mini"
      />
    </div>
    <div ref="chartCurve" style="width: 100%;height: 200px"></div>
    <div slot="footer" class="dialog-footer" style="text-align: center">
      <el-button size="mini" icon="el-icon-circle-close" @click="cancelView">关闭</el-button>
    </div>
  </el-dialog>
</template>
<script>

import * as echarts from 'echarts'

export default {
  data() {
    return {
      visible: false,
      title: '',
      pickerOptions: {
        shortcuts: [{
          text: '最近24小时',
          onClick(picker) {
            const end = new Date()
            const start = new Date()
            start.setTime(start.getTime() - 3600 * 1000 * 24)
            picker.$emit('pick', [start, end])
          }
        }, {
          text: '最近72小时',
          onClick(picker) {
            const end = new Date()
            const start = new Date()
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 3)
            picker.$emit('pick', [start, end])
          }
        }, {
          text: '最近7天',
          onClick(picker) {
            const end = new Date()
            const start = new Date()
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 7)
            picker.$emit('pick', [start, end])
          }
        }, {
          text: '最近30天',
          onClick(picker) {
            const end = new Date()
            const start = new Date()
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 30)
            picker.$emit('pick', [start, end])
          }
        }]
      },
      timeList: [],
      iptv: {},
      port: {}
    }
  },
  mounted() {
  },
  methods: {
    showView() {
      this.visible = true
    },
    hideView() {
      this.visible = false
    },
    cancelView() {
      this.hideView()
    },
    initCharts() {
      this.$nextTick(() => {
        const chartCurve = echarts.init(this.$refs.chartCurve)
        let base = +new Date(2023, 11, 1)
        const towHour = 2 * 3600 * 1000
        const inputDataList = [[base, -20]]
        for (let i = 1; i < 72; i++) {
          const now = new Date((base += towHour))
          inputDataList.push([+now, Math.round((Math.random() - 0.5) * 10 + inputDataList[i - 1][1])])
        }
        base = +new Date(2023, 11, 1)
        const outputDataList = [[base, -15]]
        for (let i = 1; i < 72; i++) {
          const now = new Date((base += towHour))
          outputDataList.push([+now, Math.round((Math.random() - 0.5) * 10 + outputDataList[i - 1][1])])
        }
        const option = {
          tooltip: {
            trigger: 'axis',
            position: function (pt) {
              return [pt[0], '10%']
            }
          },
          color: ['#4D2BD5', '#E66B1A'],
          legend: {
            textStyle: {
              color: '#666'
            }
          },
          grid: {
            left: '8px',
            right: '8px',
            top: '28px',
            bottom: '8px',
            containLabel: true
          },
          xAxis: {
            type: 'time',
            boundaryGap: false,
            axisLine: {
              show: false,
              lineStyle: {
                color: '#333'
              }
            },
            axisTick: {
              show: false
            },
            axisLabel: {
              interval: 2,
              color: '#333'
            }
          },
          yAxis: {
            type: 'value',
            axisLine: {
              show: false,
              lineStyle: {
                color: '#333'
              }
            },
            axisTick: {
              show: false
            },
            axisLabel: {
              color: '#333'
            },
            splitLine: {
              show: true,
              lineStyle: {
                color: '#eee'
              }
            }
          },
          dataZoom: [
            {
              type: 'inside'
            }
          ],
          series: [
            {
              name: '光接收功率',
              type: 'line',
              smooth: true,
              lineStyle: {
                width: 0
              },
              showSymbol: false,
              areaStyle: {
                opacity: 0.8,
                color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                  {
                    offset: 0,
                    color: 'rgba(55, 162, 255, 0.1)'
                  },
                  {
                    offset: 1,
                    color: 'rgba(116, 21, 219, 1)'
                  }
                ])
              },
              emphasis: {
                focus: 'series'
              },
              data: inputDataList
            },
            {
              name: '光发射功率',
              type: 'line',
              smooth: true,
              lineStyle: {
                width: 0
              },
              showSymbol: false,
              label: {
                show: true,
                position: 'top'
              },
              areaStyle: {
                opacity: 0.8,
                color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                  {
                    offset: 0,
                    color: 'rgba(255, 191, 0, 0.1)'
                  },
                  {
                    offset: 1,
                    color: 'rgba(224, 62, 76, 1)'
                  }
                ])
              },
              emphasis: {
                focus: 'series'
              },
              data: outputDataList
            }
          ]
        }
        chartCurve.setOption(option)
      })
    },
    loadData(iptv, port) {
      this.iptv = iptv
      this.port = port
      this.title = `${iptv.name}-${port.port} 历史曲线图`
      this.showView()
      this.$nextTick(() => {
        const end = new Date()
        const start = new Date()
        start.setTime(start.getTime() - 3600 * 1000 * 24)
        this.timeList = [start, end]
        this.initCharts()
      })
    }
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
.cell-box {
  min-width: 120px;

  .cell-input {
    width: 150px;
  }

  .cell-select {
    width: 220px;
  }

  >>> .el-input__inner {
    border: 1px solid rgba(100, 100, 100, 0.1);
    border-bottom: 1px solid rgba(100, 100, 100, 0.2);
    border-radius: 5px;
  }

  >>> .el-input.is-disabled .el-input__inner {
    border-radius: 0;
    border: 0;
    border-bottom: 1px solid rgba(100, 100, 100, 0.4);
    background: white;
    cursor: text;
  }

  >>> .el-input.is-disabled .el-input__icon {
    cursor: text;
  }

  >>> .el-icon-circle-check {
    color: #13ce66;
  }

  >>> .el-icon-arrow-up:before {
    content: '';
  }
}
</style>
