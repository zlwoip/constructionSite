<template>
  <div class="main-container">
    <div class="table-header-container">
      <el-card :body-style="{padding: '0'}" shadow="never">
        <div class="wrapper" :style="{background:waterMark?`url(${waterMark.toDataURL('image/png')}) left top repeat`:``}">
          <div class="left-wrapper">
            <span class="label">数据检索条件:</span>
            <el-input v-model="query.ip" clearable size="mini" placeholder="请输入交换机IP" style="width: 180px;margin:0 5px" />
            <el-input v-model="query.port" clearable size="mini" placeholder="请输入交换机端口" style="width: 180px;margin:0 5px" />
            <el-input v-model="query.address" clearable size="mini" placeholder="请输入端口对应地址" style="width: 180px;margin:0 5px" />
          </div>
          <div class="flex-sub">
            <el-button type="primary" size="mini" icon="el-icon-magic-stick" @click="search">过滤</el-button>
            <el-button size="mini" icon="el-icon-refresh-left" @click="resetSearch">重置</el-button>
          </div>
          <div class="right-wrapper">
            <el-button type="primary" size="mini" icon="el-icon-download" @click="loadData">拉取数据</el-button>
            <el-button type="warning" size="mini" icon="el-icon-setting" @click="toSetting">监听设置</el-button>
          </div>
        </div>
      </el-card>
    </div>
    <el-card :body-style="{padding: 0}" class="table-container" shadow="never">
      <div class="wrapper" :style="{background:waterMark?`url(${waterMark.toDataURL('image/png')}) left top repeat`:``}">
        <div class="table-card-box">
          <div class="table-card-title">
            <el-row>
              <el-col :span="20" style="padding-left: 18px">
                本次巡检共检测到<span style="color:#191970;"> 126 </span>个交换机终端，端口数据<span style="color:#0000CD;"> 428 </span>条，有效数据<span style="color:#008B00;"> 413 </span>条，异常数据<span :class="errorNum?'underline touch':'underline'" :title="errorNum?'点击查看异常清单':''" :style="{color: errorNum?'red':'#00CD66'}" @click="openErrorLog()"> {{ errorNum }} </span>条
              </el-col>
              <el-col :span="4" style="text-align: right">
                <span style="padding-right:18px; font-size: 16px;color: #666">{{ dateTime }}</span>
              </el-col>
            </el-row>
          </div>
          <div v-for="(iptv, i) in tableList" :key="'iptv_'+i" class="box-card">
            <div class="line-title">
              <div class="title-point"><img :src="require('@/assets/Routers.png')" /></div>
              <span class="title-lable">{{ iptv.name }}</span>
              <span class="title-ip"><img :src="require('@/assets/IP.png')" /> {{ iptv.ip }} </span>
              <span class="title-ups" :style="{color:iptv.ups==='电源正常'?'#00CD66':(iptv.ups==='电源异常'?'#f00':'')}"><img :src="require('@/assets/ups.png')" /> {{ iptv.ups }} </span>
              <div class="title-line"></div>
            </div>
            <div style="padding-top:10px">
              <table class="table">
                <tr class="tr">
                  <th class="th">端口<img :src="require('@/assets/port.png')" style="transform: scaleX(0.9) scaleY(0.9)" /></th>
                  <th class="th">对应地址<img :src="require('@/assets/locat.png')" /></th>
                  <th class="th">光接收功率<img :src="require('@/assets/wave.png')" /></th>
                  <th class="th">光发射功率<img :src="require('@/assets/wave.png')" /></th>
                </tr>
                <tr v-for="(port, ii) in iptv.portList" :key="'p_'+i+'_'+ii" class="tr">
                  <td>
                    <span class="color-dot" :style="{backgroundColor:colorScheme[ii]}"></span>
                    <span class="touch link-text" title="点击查看历史曲线" @click="toHistory(iptv,port)">{{ port.port }}</span>
                  </td>
                  <td>{{ port.address }}</td>
                  <td>{{ port.input }}</td>
                  <td>{{ port.output }}</td>
                </tr>
              </table>
            </div>
            <div style="width: 100%">
              <div style="width: 50%;display: inline-block;overflow: hidden;">
                <div :id="`chartPi${i}`" style="height: 180px;margin-top: -90px"></div>
              </div>
              <div style="width: 50%;display: inline-block;overflow: hidden;">
                <div :id="`chartPo${i}`" style="height: 180px;margin-top: -90px"></div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </el-card>
    <error-log ref="errorLogPage" />
    <setting-page ref="settingPage" />
    <history-page ref="historyPage" />
  </div>
</template>

<script>
import * as echarts from 'echarts'
import errorLog from './errorLog'
import settingPage from './setting'
import historyPage from './history'
export default {
  components: { errorLog, settingPage, historyPage },
  data() {
    return {
      colorScheme: [
        '#ff7875',
        '#ff9c6e',
        '#ffc069',
        '#ffd666',
        '#b37feb',
        '#727bec',
        '#3eaf7c',
        '#4a657a',
        '#223273',
        '#6f6f6f',
        '#37a2da',
        '#67e0e3',
        '#9fe6b8',
        '#ffdb5c',
        '#ff9f7f',
        '#e062ae',
        '#e690d1',
        '#e7bcf3',
        '#9d96f5',
        '#8378EA'
      ],
      waterMark: '',
      dateTime: '',
      errorNum: 3,
      query: {
        ip: '',
        port: '',
        address: ''
      },
      dataList: [
        {
          name: 'IPTV环网-威海7503',
          ip: '10.253.174.240',
          ups: '电源正常',
          errorNum: 0,
          portList: [
            { port: 'XGE0/0/25', address: '威海6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/25', address: '威海6520', input: -5.19, output: -1.78 },
            { port: 'GE0/0/3', address: '荣成6520', input: -5.19, output: -1.78 },
            { port: 'GE0/0/1', address: '荣成6520', input: -5.19, output: -1.78 },
            { port: 'XGE2/0/2', address: '文登6520', input: -5.19, output: -1.78 },
            { port: 'XGE2/0/1', address: '乳山6520', input: -5.19, output: -1.78 }
          ]
        },
        {
          name: 'IPTV环网-文登6520',
          ip: '10.253.174.225',
          ups: '暂不支持',
          errorNum: 0,
          portList: [
            { port: 'XGE1/0/6', address: '威海7503', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/22', address: '荣成6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/21', address: '乳山6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/23', address: '文登7604', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/24', address: '文登7604', input: -5.19, output: -1.78 }
          ]
        },
        {
          name: 'IPTV环网-荣成6520',
          ip: '10.253.174.193',
          ups: '电源异常',
          errorNum: 0,
          portList: [
            { port: 'XGE1/0/1', address: '威海7503', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/9', address: '威海7503', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/22', address: '文登6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/23', address: '荣成7604', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/24', address: '荣成7604', input: -5.19, output: -1.78 }
          ]
        },
        {
          name: 'IPTV环网-威海7503',
          ip: '10.253.174.240',
          ups: '电源正常',
          errorNum: 0,
          portList: [
            { port: 'XGE0/0/25', address: '威海6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/25', address: '威海6520', input: -5.19, output: -1.78 },
            { port: 'GE0/0/3', address: '荣成6520', input: -5.19, output: -1.78 },
            { port: 'GE0/0/1', address: '荣成6520', input: -5.19, output: -1.78 },
            { port: 'XGE2/0/2', address: '文登6520', input: -5.19, output: -1.78 },
            { port: 'XGE2/0/1', address: '乳山6520', input: -5.19, output: -1.78 }
          ]
        },
        {
          name: 'IPTV环网-文登6520',
          ip: '10.253.174.225',
          ups: '暂不支持',
          errorNum: 0,
          portList: [
            { port: 'XGE1/0/6', address: '威海7503', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/22', address: '荣成6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/21', address: '乳山6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/23', address: '文登7604', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/24', address: '文登7604', input: -5.19, output: -1.78 }
          ]
        },
        {
          name: 'IPTV环网-威海7503',
          ip: '10.253.174.240',
          ups: '电源正常',
          errorNum: 0,
          portList: [
            { port: 'XGE0/0/25', address: '威海6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/25', address: '威海6520', input: -5.19, output: -1.78 },
            { port: 'GE0/0/3', address: '荣成6520', input: -5.19, output: -1.78 },
            { port: 'GE0/0/1', address: '荣成6520', input: -5.19, output: -1.78 },
            { port: 'XGE2/0/2', address: '文登6520', input: -5.19, output: -1.78 },
            { port: 'XGE2/0/1', address: '乳山6520', input: -5.19, output: -1.78 }
          ]
        },
        {
          name: 'IPTV环网-文登6520',
          ip: '10.253.174.225',
          ups: '暂不支持',
          errorNum: 0,
          portList: [
            { port: 'XGE1/0/6', address: '威海7503', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/22', address: '荣成6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/21', address: '乳山6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/23', address: '文登7604', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/24', address: '文登7604', input: -5.19, output: -1.78 }
          ]
        },
        {
          name: 'IPTV环网-荣成6520',
          ip: '10.253.174.193',
          ups: '电源异常',
          errorNum: 0,
          portList: [
            { port: 'XGE1/0/1', address: '威海7503', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/9', address: '威海7503', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/22', address: '文登6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/23', address: '荣成7604', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/24', address: '荣成7604', input: -5.19, output: -1.78 }
          ]
        },
        {
          name: 'IPTV环网-威海7503',
          ip: '10.253.174.240',
          ups: '电源正常',
          errorNum: 0,
          portList: [
            { port: 'XGE0/0/25', address: '威海6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/25', address: '威海6520', input: -5.19, output: -1.78 },
            { port: 'GE0/0/3', address: '荣成6520', input: -5.19, output: -1.78 },
            { port: 'GE0/0/1', address: '荣成6520', input: -5.19, output: -1.78 },
            { port: 'XGE2/0/2', address: '文登6520', input: -5.19, output: -1.78 },
            { port: 'XGE2/0/1', address: '乳山6520', input: -5.19, output: -1.78 }
          ]
        },
        {
          name: 'IPTV环网-文登6520',
          ip: '10.253.174.225',
          ups: '暂不支持',
          errorNum: 0,
          portList: [
            { port: 'XGE1/0/6', address: '威海7503', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/22', address: '荣成6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/21', address: '乳山6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/23', address: '文登7604', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/24', address: '文登7604', input: -5.19, output: -1.78 }
          ]
        },
        {
          name: 'IPTV环网-荣成6520',
          ip: '10.253.174.193',
          ups: '电源异常',
          errorNum: 0,
          portList: [
            { port: 'XGE1/0/1', address: '威海7503', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/9', address: '威海7503', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/22', address: '文登6520', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/23', address: '荣成7604', input: -5.19, output: -1.78 },
            { port: 'XGE1/0/24', address: '荣成7604', input: -5.19, output: -1.78 }
          ]
        }
      ],
      tableList: [],
      chartsList: []
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.dateTime = this.getNowDateTime()
      this.tableList = this.dataList
      const canvas = document.createElement('canvas')
      const ctx = canvas.getContext('2d')
      canvas.width = canvas.height = 160
      ctx.textAlign = 'center'
      ctx.textBaseline = 'middle'
      ctx.globalAlpha = 0.08
      ctx.font = '20px Microsoft Yahei'
      ctx.translate(80, 80)
      ctx.rotate(-Math.PI / 4)
      ctx.fillText(`威海广电网络`, 0, 0)
      this.waterMark = canvas
      this.initCharts()
    })
  },
  methods: {
    randNum(min, max, len, isRound) {
      var arr = []
      for (let i = 0; i < len; i++) {
        var num
        num = Math.random() * (max - min) + min
        if (isRound) {
          num = Math.round(num)
          arr.push(num)
        } else {
          arr.push(num.toFixed(2))
        }
      }
      return arr
    },
    getNowDateTime(now) {
      if (!now) {
        now = new Date()
      }
      return `${now.getFullYear()}-${(now.getMonth() + 1).toString().padStart(2, '0')}-${now.getDate().toString().padStart(2, '0')} ${now.getHours().toString().padStart(2, '0')}:${now.getMinutes().toString().padStart(2, '0')}:${now.getSeconds().toString().padStart(2, '0')}`
    },
    openErrorLog() {
      if (this.errorNum) {
        this.$refs.errorLogPage.loadData({ dateTime: this.dateTime })
      }
    },
    search() {
      this.tableList = JSON.parse(JSON.stringify(this.dataList))
      if (this.query.ip) {
        this.tableList = this.tableList.filter(item => {
          return item.ip.indexOf(this.query.ip) >= 0
        })
      }
      if (this.query.port) {
        this.tableList.forEach(iptv => {
          iptv.portList = iptv.portList.filter(item => {
            return item.port.indexOf(this.query.port) >= 0
          })
        })
        this.tableList = this.tableList.filter(item => {
          return item.portList.length
        })
      }
      if (this.query.address) {
        this.tableList.forEach(iptv => {
          iptv.portList = iptv.portList.filter(item => {
            return item.address.indexOf(this.query.address) >= 0
          })
        })
        this.tableList = this.tableList.filter(item => {
          return item.portList.length
        })
      }
      this.initCharts()
    },
    resetSearch() {
      this.query.ip = ''
      this.query.port = ''
      this.query.address = ''
      this.tableList = this.dataList
      this.initCharts()
    },
    toSetting() {
      this.$refs.settingPage.loadData()
    },
    toHistory(iptv, port) {
      this.$refs.historyPage.loadData(iptv, port)
    },
    initCharts() {
      this.$nextTick(() => {
        this.tableList.forEach((iptv, index) => {
          const dataList = []
          let sum = 0
          iptv.portList.forEach(item => {
            const rand = this.randNum(10, 30, 1, true)[0]
            sum += rand
            dataList.push({
              value: rand,
              name: item.port
            })
          })
          dataList.push({
            value: sum,
            itemStyle: {
              color: 'none',
              decal: {
                symbol: 'none'
              }
            },
            label: {
              show: false
            }
          })
          const chart = echarts.init(document.getElementById(`chartPi${index}`))
          const option = {
            title: {
              show: true,
              text: '光接收功率分布',
              left: 'center',
              bottom: '-6',
              textStyle: {
                color: '#999',
                fontSize: '12'
              }
            },
            color: this.colorScheme,
            tooltip: {
              trigger: 'item',
              position: ['24%', '70%']
            },
            legend: {
              show: false
            },
            grid: {
              left: '0px',
              right: '0px',
              top: '0px',
              bottom: '0px',
              containLabel: false
            },
            series: [
              {
                type: 'pie',
                roseType: 'radius',
                radius: ['70%', '100%'],
                center: ['50%', '100%'],
                startAngle: 180,
                label: {
                  show: false
                },
                data: dataList
              }
            ]
          }
          chart.setOption(option)
        })
        this.tableList.forEach((iptv, index) => {
          const dataList = []
          let sum = 0
          iptv.portList.forEach(item => {
            const rand = this.randNum(10, 30, 1, true)[0]
            sum += rand
            dataList.push({
              value: rand,
              name: item.port
            })
          })
          dataList.push({
            value: sum,
            itemStyle: {
              color: 'none',
              decal: {
                symbol: 'none'
              }
            },
            label: {
              show: false
            }
          })
          const chart = echarts.init(document.getElementById(`chartPo${index}`))
          const option = {
            title: {
              show: true,
              text: '光发射功率分布',
              left: 'center',
              bottom: '-6',
              textStyle: {
                color: '#999',
                fontSize: '12'
              }
            },
            color: this.colorScheme,
            tooltip: {
              trigger: 'item',
              position: ['24%', '70%']
            },
            legend: {
              show: false
            },
            grid: {
              left: '0px',
              right: '0px',
              top: '0px',
              bottom: '0px',
              containLabel: false
            },
            series: [
              {
                type: 'pie',
                roseType: 'radius',
                radius: ['70%', '100%'],
                center: ['50%', '100%'],
                startAngle: 180,
                label: {
                  show: false
                },
                data: dataList
              }
            ]
          }
          chart.setOption(option)
        })
      })
    },
    loadData() {

    }
  }
}
</script>

<style lang="scss" scoped>
.box-card {
  display: inline-block;
  vertical-align: top;
  position: relative;
  margin: 6px 12px;
  padding: 10px 6px;
  width: 48%;
  background: #FFFFFF;
  border: 1px solid rgba(0,0,0,0.12);
  box-shadow: 1px 2px 8px 0 rgba(0,0,0,0.12);
  border-radius: 10px;
  .line-title {
    padding: 0;
    position: relative;
    border-bottom: 3px solid #0457C7;
    font-size: 16px;
    color: #333333;
    .title-point {
      position: absolute;
      top: 1px;
    }
    .title-lable {
      padding-left: 26px;
      font-weight: bolder;
    }
    .title-ip {
      float: right;
      padding-right: 5px;
      color: #999999;
      font-size: 14px;
      img {
        position: relative;
        top: 3px;
      }
    }
    .title-ups {
      float: right;
      padding-right: 20px;
      color: #999999;
      font-size: 14px;
      img {
        position: relative;
        top: 3px;
      }
    }
    .title-line{
      width: 100%;
      position: absolute;
      bottom: -7px;
      border-bottom: 2px solid #1961C5;
      opacity: 0.35
    }
  }
}
.table-card-box {
  .table-card-title {
    width: 100%;
    padding: 2px;
    margin-bottom: 10px;
    color: #4e4e4e;
    font-weight: bold;
    font-size: 14px;
    background-color: rgba(0,0,0,0.1);
    span {
      font-size: 18px;
    }
  }
  table {
    border-collapse: collapse;
    width: 100%;
    text-align: left;
    border-radius: 5px;
  }
  th, td {
    border: 1px solid #ddd;
    padding: 8px;
    border-radius: 5px;
    text-align: center;
    img {
      position: absolute;
      padding: 0 4px;
    }
  }
  th {
    background-color: #e2e2e2;
    font-weight: bold;
  }
  .bk {
    background-color: #f2f2f2;
  }
}
.table-header-container {
  .wrapper {
    height: 50px;
    display: flex;
    flex-direction: row;
    align-items: center;

    .left-wrapper {
      margin-left: 15px;
    }

    .flex-sub {
      padding: 0 20px;
    }

    .right-wrapper {
      margin-right: 15px;
    }

    .label {
      padding: 0 10px;
    }
  }

  .search-content-wrapper {
    padding: 10px;

    .search-item-wrapper {
      justify-content: center;
      align-items: center;

      & > span {
        color: #606266;
        font-size: 14px;
        font-weight: 500;
        margin-right: 8px;
      }

      .form-item {
        flex: 1;
      }
    }
  }
}
.table-container {
  .wrapper {
    height: 70vh;
    padding: 4px 20px;
    overflow-y: auto;
  }
}
</style>
