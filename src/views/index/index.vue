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
            <OrderChart ref="mOrderChart" />
          </template>
        </DataItem>
      </el-col>
    </el-row>
    <el-row :gutter="5" class="margin-top-xs">
      <el-col :xs="24" :sm="24" :md="6">
        <div class="flex flex-direction">
          <SalesChart ref="salesChart" />
          <StudentChart ref="studentChart" class="margin-top-xs" />
        </div>
      </el-col>
      <el-col :xs="24" :sm="24" :md="12" class="map-margin-tb">
        <div>
          <SchoolChart ref="schoolChart" />
        </div>
      </el-col>
      <el-col :xs="24" :sm="24" :md="6">
        <div class="flex flex-direction">
          <EnrollmentChannelsChart ref="enrollmentChannelsChart" />
          <DepartmentChart ref="departmentChart" class="margin-top-xs" />
        </div>
      </el-col>
    </el-row>
    <el-row :gutter="5" class="margin-top-xs">
      <el-col :span="24">
        <FullYearSalesChart ref="fullYearSalesChart" />
      </el-col>
    </el-row>
  </div>
</template>

<script>
import DataItem from './components/DataItem'
import OrderChart from './components/chart/OrderChart'
import SalesChart from './components/chart/SalesChart'
import StudentChart from './components/chart/StudentChart'
import EnrollmentChannelsChart from './components/chart/EnrollmentChannelsChart'
import DepartmentChart from './components/chart/DepartmentChart'
import SchoolChart from './components/chart/SchoolChart'
import FullYearSalesChart from './components/chart/FullYearSalesChart'
import ResizeMixin from '@/mixins/ResizeMixin'
import * as echarts from 'echarts'
export default {
  name: 'Main',
  components: {
    DataItem,
    OrderChart,
    SchoolChart,
    SalesChart,
    StudentChart,
    EnrollmentChannelsChart,
    DepartmentChart,
    FullYearSalesChart
  },
  mixins: [ResizeMixin],
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
          title: '今日当班技术员',
          data: '189笔',
          bottomTitle: '累计订单量',
          totalSum: '1万+',
          extra: {
            data: 80
          }
        }
      ],
      operatorChart: null,
      dataTimer: null
    }
  },
  computed: {
    collapse() {
      return this.$layoutStore.state.isCollapse
    }
  },
  watch: {
    collapse(newVal) {
      setTimeout(() => {
        this.onResize()
      }, 500)
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.operatorChart = echarts.init(document.getElementById('operatorChart'))
      window.addEventListener('resize', () => {
        this.operatorChart.resize()
      })
      this.initChart()
      this.dataTimer = setInterval(() => {
        this.initChart()
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
    initChart() {
      const names = ['张三', '李四', '王五', '赵六']
      this.$nextTick(() => {
        const getDateTime = (now) => {
          if (!now) {
            now = new Date()
          }
          return `${now.getHours().toString().padStart(2, '0')}:${now.getMinutes().toString().padStart(2, '0')}:${now.getSeconds().toString().padStart(2, '0')}`
        }
        const now = new Date()
        const today = new Date(now.getFullYear(), now.getMonth(), now.getDate())
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
          tooltip: {
            trigger: 'axis',
            formatter(params) {
              if (params[1].data && params[0].data) {
                return `<div>${params[1].axisValue}：${getDateTime(new Date(params[1].data))} ~ ${getDateTime(new Date(params[0].data))}</div>`
              }
              return ''
            },
            confine: true,
            axisPointer: {
              type: 'shadow'
            }
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
                  color: 'rgba(0, 0, 0, 0.3)'
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
      })
    },
    onResize(width) {
      this.$refs.mOrderChart[0].updateChart()
      this.$refs.salesChart.updateChart()
      this.$refs.departmentChart.updateChart()
      this.$refs.enrollmentChannelsChart.updateChart()
      this.$refs.schoolChart.updateChart()
      this.$refs.studentChart.updateChart()
      this.$refs.fullYearSalesChart.updateChart()
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
