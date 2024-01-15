<template>
  <div class="main-container">
    <el-row :gutter="5">
      <el-col v-for="(item, index) of dataList" :key="index" :xs="24" :sm="12" :md="6" class="item-wrapper">
        <DataItem :data-model="item">
          <template v-if="index === 0" #extra="{ extra }">
            <div class="margin-top" style="height: 54px">
              <div class="text-gray">
                巡检端口数：{{ extra.sum }}
              </div>
              <div class="text-gray margin-top-sm">
                异常端口数：{{ extra.error }}
                <i v-if="extra.isUp" class="el-icon-caret-top text-blue"></i>
              </div>
            </div>
          </template>
          <template v-else-if="index === 1" #extra="{ extra }">
            <div class="margin-top" style="position: relative;height: 54px">
              <div class="text-gray">
                巡检ups数：{{ extra.sum }}
              </div>
              <div class="text-gray margin-top-sm">
                异常ups数：{{ extra.error }}
                <i v-if="extra.isUp" class="el-icon-caret-top text-blue"></i>
              </div>
              <div class="stack-avatar-wrapper">
                <img :src="require(`@/assets/signImg/${extra.sign}.png`)" title="巡检员电子签名" style="height: 36px;margin-right: 30px" />
              </div>
            </div>
          </template>
          <template v-else-if="index === 2" #extra>
            <div id="operatorChart" style="width: 100%;height: 100px"></div>
          </template>
          <template v-else-if="index === 3" #extra>
            <div id="alarmChart" style="width: 100%;height: 69px"></div>
          </template>
        </DataItem>
      </el-col>
    </el-row>

  </div>
</template>

<script>
import DataItem from './components/DataItem'
import * as echarts from 'echarts'

export default {
  name: 'Main',
  components: {
    DataItem
  },
  data() {
    return {
      dataList: [
        {
          title: '最近一次交换机巡检',
          data: '00:00:00',
          bottomTitle: '已累计巡检',
          totalSum: '0次',
          extra: {
            sum: 0,
            error: 0,
            isUp: false
          }
        },
        {
          title: '最近一次ups巡检',
          data: '00:00:00',
          bottomTitle: '已累计巡检',
          totalSum: '0次',
          extra: {
            sum: 0,
            error: 0,
            isUp: false,
            sign: 'signTemp'
          }
        },
        {
          title: '今日当班值机员',
          data: '',
          bottomTitle: '当前坐班人',
          totalSum: ''
        },
        {
          title: '近一周光功率报警统计',
          data: '189次',
          bottomTitle: '今日发生报警',
          totalSum: '0次'
        }
      ],
      operatorChart: null,
      alarmChart: null,
      dataTimer: null
    }
  },
  mounted() {
    this.$nextTick(() => {
      const lastSBIResult = localStorage.getItem('lastSBIResult')
      if (lastSBIResult) {
        const lastSBIResultObj = JSON.parse(lastSBIResult)
        this.dataList[0].data = lastSBIResultObj.dateTime.split(' ')[1]
        this.dataList[0].extra.sum = lastSBIResultObj.dataNum
        this.dataList[0].extra.error = lastSBIResultObj.errorNum
        this.dataList[0].totalSum = lastSBIResultObj.count + '次'
      }
      const lastUPSResult = localStorage.getItem('lastUPSResult')
      if (lastUPSResult) {
        const lastUPSResultObj = JSON.parse(lastUPSResult)
        this.dataList[1].data = lastUPSResultObj.dateTime.split(' ')[1]
        this.dataList[1].extra.sum = lastUPSResultObj.dataNum
        this.dataList[1].extra.error = lastUPSResultObj.errorNum
        this.dataList[1].extra.sign = lastUPSResultObj.signImg
        this.dataList[1].totalSum = lastUPSResultObj.count + '次'
      }
      this.operatorChart = echarts.init(document.getElementById('operatorChart'))
      this.alarmChart = echarts.init(document.getElementById('alarmChart'))
      window.addEventListener('resize', () => {
        this.operatorChart.resize()
        this.alarmChart.resize()
      })
      this.initChartOPT()
      this.initChartARM()
      this.dataTimer = setInterval(() => {
        this.initChartOPT()
        this.initChartARM()
      }, 1000 * 300)
    })
  },
  destroyed() {
    if (this.dataTimer) {
      clearInterval(this.dataTimer)
      this.dataTimer = null
    }
  },
  methods: {
    initChartOPT() {
      const names = []
      this.$nextTick(() => {
        const now = new Date()
        const today = new Date(now.getFullYear(), now.getMonth(), now.getDate())
        const todayStr = `${now.getFullYear()}-${(now.getMonth() + 1).toString().padStart(2, '0')}-${now.getDate().toString().padStart(2, '0')}`
        this.$post({
          url: this.$urlPath.showClassesList,
          data: {
            startDate: todayStr,
            endDate: todayStr
          }
        }).then((res) => {
          const classesList = res.classesList || []
          if (classesList.length) {
            names.push(classesList[0].operatorOne)
            names.push(classesList[0].operatorTwo)
            names.push(classesList[0].operatorThree)
            names.push(classesList[0].operatorDay)
          }
          if (now.getHours() >= 23) { // 当天超过23点算进入下一天
            today.setTime(today.getTime() + 3600000 * 24)
          }
          const beginList = [today.getTime() - 3600000, today.getTime() + 3600000 * 7, today.getTime() + 3600000 * 15, today.getTime() + 3600000 * 8.5]
          const endList = [today.getTime() + 3600000 * 7, today.getTime() + 3600000 * 15, today.getTime() + 3600000 * 23, today.getTime() + 3600000 * 17]
          const colors = []
          const workNames = []
          for (let i = 0; i < 4; i++) {
            if (now.getTime() >= beginList[i] && now.getTime() <= endList[i]) {
              colors.push('#4169E1')
              workNames.push(names[i])
            } else {
              colors.push('#CFCFCF')
            }
          }
          this.dataList[2].totalSum = workNames.join('、')
          const option = {
            animation: false,
            color: colors,
            // tooltip: {
            //   trigger: 'axis',
            //   formatter(params) {
            //     if (params[1].data && params[0].data) {
            //       return `<div>${params[1].axisValue}：${getDateTime(new Date(params[1].data))} ~ ${getDateTime(new Date(params[0].data))}</div>`
            //     }
            //     return ''
            //   },
            //   confine: true,
            //   axisPointer: {
            //     type: 'shadow'
            //   }
            // },
            tooltip: {
              trigger: 'item',
              axisPointer: {
                type: 'cross',
                label: {
                  backgroundColor: '#6a7985'
                }
              },
              showContent: false
            },
            grid: {
              containLabel: true,
              show: false,
              right: 0,
              left: 0,
              bottom: 0,
              top: 0,
              backgroundColor: '#fff'
            },
            xAxis: {
              type: 'time',
              position: 'top',
              axisLabel: {
                margin: 0
              },
              axisTick: {
                // 隐藏刻度
                show: false
              },
              axisLine: {
                // 隐藏轴线
                show: false
              },
              splitLine: {
                // 显示网格线
                show: false
              }
            },
            yAxis: {
              inverse: true, // y 轴数据翻转，该操作是为了保证项目一放在最上面，项目七在最下面
              axisTick: {
                // 隐藏刻度
                show: false
              },
              axisLine: {
                // 隐藏轴线
                show: false
              },
              data: names
            },
            series: [
              {
                name: '持续时间',
                type: 'bar',
                stack: 'duration',
                colorBy: 'data',
                itemStyle: {
                  borderColor: '#fff',
                  borderWidth: 1
                },
                zlevel: -1,
                markLine: {
                  silent: false,
                  emphasis: {
                    disabled: true
                  },
                  label: {
                    show: false
                  },
                  symbol: 'none',
                  itemStyle: {
                    color: 'rgba(255, 0, 0, 0.6)'
                  },
                  data: [
                    {
                      xAxis: now.getTime()
                    }
                  ]
                },
                data: endList
              },
              {
                name: '持续时间',
                type: 'bar',
                stack: 'duration',
                itemStyle: {
                  color: '#fff'
                },
                zlevel: -1,
                z: 9,
                data: beginList
              }
            ]
          }
          this.operatorChart.setOption(option)
        }).catch((error) => {
          this.$errorMsg(error || '接口调用失败，未知异常')
        })
      })
    },
    initChartARM() {
      this.$nextTick(() => {
        this.$post({
          url: this.$urlPath.showAlarmLast7DayCount
        }).then((res) => {
          const alarmCountList = res.alarmCountList
          const now = new Date()
          const tempDay = new Date(now.getFullYear(), now.getMonth(), now.getDate())
          let dataList = []
          for (let i = 0; i < 7; i++) {
            dataList.push({
              label: `${tempDay.getFullYear()}-${(tempDay.getMonth() + 1).toString().padStart(2, '0')}-${tempDay.getDate().toString().padStart(2, '0')}`,
              value: 0
            })
            tempDay.setTime(tempDay.getTime() - 3600000 * 24)
          }
          dataList.forEach(item => {
            for (let i = 0; i < alarmCountList.length; i++) {
              if (item.label === alarmCountList[i].date) {
                item.value = Number(alarmCountList[i].data_count) || 0
                break
              }
            }
          })
          this.dataList[3].totalSum = dataList[0].value + '次'
          dataList = dataList.reverse()
          let sum = 0
          dataList.forEach(item => {
            sum += item.value
          })
          this.dataList[3].data = sum + '次'
          const option = {
            animation: false,
            tooltip: {
              trigger: 'axis',
              confine: true,
              axisPointer: {
                type: 'line',
                label: {
                  backgroundColor: '#6a7985'
                }
              }
            },
            grid: {
              containLabel: false,
              show: false,
              right: 0,
              left: 0,
              bottom: 0,
              top: 0,
              backgroundColor: '#fff'
            },
            xAxis: {
              type: 'category',
              data: dataList.map(item => {
                return item.label
              }),
              splitLine: { show: false }
            },
            yAxis: [
              {
                type: 'value',
                splitLine: { show: false }
              }
            ],
            series: [
              {
                data: dataList.map(item => {
                  return item.value
                }),
                type: 'line',
                smooth: true,
                lineStyle: {
                  width: 0
                },
                showSymbol: false,
                areaStyle: {
                  opacity: 0.8,
                  color: new this.$echarts.graphic.LinearGradient(0, 0, 0, 1, [
                    {
                      offset: 0,
                      color: 'rgba(128, 255, 165)'
                    },
                    {
                      offset: 1,
                      color: 'rgba(1, 191, 236)'
                    }
                  ])
                }
              }
            ]
          }
          this.alarmChart.setOption(option)
        }).catch((error) => {
          this.$errorMsg(error || '接口调用失败，未知异常')
        })
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.chart-item {
  background-color: #fff;
}
.stack-avatar-wrapper {
  position: absolute;
  right: -2%;
  top: 10%;
}
.avatar {
  width: 35px;
  height: 35px;
  object-fit: cover;
  border-radius: 50%;
  border: 1px solid #f5f5f5;
}
</style>
