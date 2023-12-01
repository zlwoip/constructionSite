<template>
  <div class="main-container">
    <div class="table-header-container">
      <el-card :body-style="{padding: '0'}" shadow="never">
        <div class="wrapper" :style="{background:waterMark?`url(${waterMark.toDataURL('image/png')}) left top repeat`:``}">
          <div class="left-wrapper">
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
          <div class="flex-sub">
            <el-button type="primary" size="mini" icon="el-icon-search">查询</el-button>
          </div>
          <div class="right-wrapper">
            <el-button type="primary" size="mini" icon="el-icon-plus" @click="toAdd">新增巡检</el-button>
            <el-button type="warning" size="mini" icon="el-icon-setting" @click="toSetting">监听设置</el-button>
          </div>
        </div>
      </el-card>
    </div>
    <el-card :body-style="{padding: 0}" class="table-container" shadow="never">
      <div class="wrapper" :style="{background:waterMark?`url(${waterMark.toDataURL('image/png')}) left top repeat`:``}">
        <el-timeline>
          <el-timeline-item v-for="(item,index) in tableList" :key="'tm_'+index" :timestamp="item.dateTime" placement="top">
            <el-card v-if="item.completed">
              <div style="font-size: 16px;color: #999999">
                <span style="font-size: 20px;font-weight: bold;color: black">{{ item.name }}</span>
                完成巡检
                <i v-if="item.errorNum===0" class="el-icon-success" style="color:#00CD66"></i>
                <i v-if="item.errorNum>0" class="el-icon-warning" style="color:#FF8C00"></i>
              </div>
              <div class="expand-btn touch" @click="expandTable(item)">
                <span v-if="item.expand" title="收起">收起 <i class="el-icon-d-arrow-right" style="transform: rotate(90deg)"></i></span>
                <span v-else title="点击查看巡检报告">展开 <i class="el-icon-d-arrow-right"></i></span>
              </div>
              <img class="signImg" :src="require('@/assets/signImg.png')" />
              <p>本次巡检共发现
                <span
                  :class="item.errorNum?'underline touch':'underline'"
                  :title="item.errorNum?'点击查看异常清单':''"
                  :style="{fontSize:'22px',fontWeight:'bold',color: item.errorNum?'red':'#00CD66'}"
                  @click="openErrorLog(item)"
                >
                  {{ item.errorNum }}
                </span>
                个异常
              </p>
              <div v-show="item.expand">
                <div class="table-card-box">
                  <div class="table-card-title">总前端120KVA-UPS系统</div>
                  <table class="table">
                    <tr class="tr">
                      <th class="th">电源</th>
                      <th class="th">温度 (℃)</th>
                      <th class="th">输入电压A (V)</th>
                      <th class="th">输入电压B (V)</th>
                      <th class="th">输入电压C (V)</th>
                      <th class="th">输出电压A (V)</th>
                      <th class="th">输出电压B (V)</th>
                      <th class="th">输出电压C (V)</th>
                      <th class="th">负载A (%)</th>
                      <th class="th">负载B (%)</th>
                      <th class="th">负载C (%)</th>
                    </tr>
                    <tr v-for="(n0, i0) in nodelist0" :key="'n_'+index+'_'+i0" class="tr">
                      <td :class="'td '+ n0.bkc||''">{{ n0.name }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.heat }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.inputA }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.inputB }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.inputC }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.outputA }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.outputB }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.outputC }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.loadA }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.loadB }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.loadC }}</td>
                    </tr>
                  </table>
                </div>
                <div class="table-card-box">
                  <div class="table-card-title">一级分前端10KVA-UPS系统</div>
                  <table class="table">
                    <tr class="tr">
                      <th class="th">分公司</th>
                      <th class="th">分前端</th>
                      <th class="th">温度 (℃)</th>
                      <th class="th">输入电压 (V)</th>
                      <th class="th">输出电压 (V)</th>
                      <th class="th">负载 (%)</th>
                    </tr>
                    <tr v-for="(n1, i1) in nodelist1" :key="'n_'+index+'_'+i1" class="tr">
                      <td v-if="n1.rowspan" :class="'td '+ n1.bkc||''" :rowspan="n1.rowspan">{{ n1.org }}</td>
                      <td :class="'td '+ n1.bkc||''">{{ n1.name }}</td>
                      <td :class="'td '+ n1.bkc||''">{{ n1.heat }}</td>
                      <td :class="'td '+ n1.bkc||''">{{ n1.input }}</td>
                      <td :class="'td '+ n1.bkc||''">{{ n1.output }}</td>
                      <td :class="'td '+ n1.bkc||''">{{ n1.load }}</td>
                    </tr>
                  </table>
                </div>
                <div class="table-card-box">
                  <div class="table-card-title">二级分前端及乡镇广播站10KVA-UPS系统</div>
                  <table class="table">
                    <tr class="tr">
                      <th class="th">分公司</th>
                      <th class="th">分前端</th>
                      <th class="th">温度 (℃)</th>
                      <th class="th">输入电压 (V)</th>
                      <th class="th">输出电压 (V)</th>
                      <th class="th">负载 (%)</th>
                    </tr>
                    <tr v-for="(n2, i2) in nodelist2" :key="'n_'+index+'_'+i2" class="tr">
                      <td v-if="n2.rowspan" :class="'td '+ n2.bkc||''" :rowspan="n2.rowspan">{{ n2.org }}</td>
                      <td :class="'td '+ n2.bkc||''">{{ n2.name }}</td>
                      <td :class="'td '+ n2.bkc||''">{{ n2.heat }}</td>
                      <td :class="'td '+ n2.bkc||''">{{ n2.input }}</td>
                      <td :class="'td '+ n2.bkc||''">{{ n2.output }}</td>
                      <td :class="'td '+ n2.bkc||''">{{ n2.load }}</td>
                    </tr>
                  </table>
                </div>
              </div>
            </el-card>
            <el-card v-else>
              <div style="font-size: 16px;color: #999999">
                <span style="font-size: 20px;font-weight: bold;color: black">{{ item.name }}</span>
                <span v-if="item.status===1"> 等待巡检... </span>
                <span v-if="item.status===2"> 正在巡检中... </span>
                <span v-if="item.status===3"> 请<span style="font-weight:bold;color:#666">确认</span>后<span style="font-weight:bold;color:#666">提交</span>该报告！ </span>
                <el-button v-if="item.status===1" type="primary" size="mini" icon="el-icon-video-play" style="float:right;margin:0 20px" @click="beginInspection(item)">开始巡检</el-button>
                <el-button v-if="item.status===2" disabled size="mini" icon="el-icon-loading" style="float:right;margin:0 20px">巡检中...</el-button>
                <el-button v-if="item.status===3" type="success" size="mini" icon="el-icon-finished" style="float:right;margin:0 20px" @click="submitInspection(item)">确认提交</el-button>
              </div>
              <div v-show="item.expand" style="margin-top:12px">
                <div class="table-card-box">
                  <div class="table-card-title">总前端120KVA-UPS系统</div>
                  <table class="table">
                    <tr class="tr">
                      <th class="th">电源</th>
                      <th class="th">温度 (℃)</th>
                      <th class="th">输入电压A (V)</th>
                      <th class="th">输入电压B (V)</th>
                      <th class="th">输入电压C (V)</th>
                      <th class="th">输出电压A (V)</th>
                      <th class="th">输出电压B (V)</th>
                      <th class="th">输出电压C (V)</th>
                      <th class="th">负载A (%)</th>
                      <th class="th">负载B (%)</th>
                      <th class="th">负载C (%)</th>
                    </tr>
                    <tr v-for="(n0, i0) in nodelist0" :key="'n_'+index+'_'+i0" class="tr">
                      <td :class="'td '+ n0.bkc||''">{{ n0.name }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.heat }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.inputA }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.inputB }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.inputC }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.outputA }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.outputB }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.outputC }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.loadA }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.loadB }}</td>
                      <td :class="'td '+ n0.bkc||''">{{ n0.loadC }}</td>
                    </tr>
                  </table>
                </div>
                <div class="table-card-box">
                  <div class="table-card-title">一级分前端10KVA-UPS系统</div>
                  <table class="table">
                    <tr class="tr">
                      <th class="th">分公司</th>
                      <th class="th">分前端</th>
                      <th class="th">温度 (℃)</th>
                      <th class="th">输入电压 (V)</th>
                      <th class="th">输出电压 (V)</th>
                      <th class="th">负载 (%)</th>
                    </tr>
                    <tr v-for="(n1, i1) in nodelist1" :key="'n_'+index+'_'+i1" class="tr">
                      <td v-if="n1.rowspan" :class="'td '+ n1.bkc||''" :rowspan="n1.rowspan">{{ n1.org }}</td>
                      <td :class="'td '+ n1.bkc||''">{{ n1.name }}</td>
                      <td :class="'td '+ n1.bkc||''">{{ n1.heat }}</td>
                      <td :class="'td '+ n1.bkc||''">{{ n1.input }}</td>
                      <td :class="'td '+ n1.bkc||''">{{ n1.output }}</td>
                      <td :class="'td '+ n1.bkc||''">{{ n1.load }}</td>
                    </tr>
                  </table>
                </div>
                <div class="table-card-box">
                  <div class="table-card-title">二级分前端及乡镇广播站10KVA-UPS系统</div>
                  <table class="table">
                    <tr class="tr">
                      <th class="th">分公司</th>
                      <th class="th">分前端</th>
                      <th class="th">温度 (℃)</th>
                      <th class="th">输入电压 (V)</th>
                      <th class="th">输出电压 (V)</th>
                      <th class="th">负载 (%)</th>
                    </tr>
                    <tr v-for="(n2, i2) in nodelist2" :key="'n_'+index+'_'+i2" class="tr">
                      <td v-if="n2.rowspan" :class="'td '+ n2.bkc||''" :rowspan="n2.rowspan">{{ n2.org }}</td>
                      <td :class="'td '+ n2.bkc||''">{{ n2.name }}</td>
                      <td :class="'td '+ n2.bkc||''">{{ n2.heat }}</td>
                      <td :class="'td '+ n2.bkc||''">{{ n2.input }}</td>
                      <td :class="'td '+ n2.bkc||''">{{ n2.output }}</td>
                      <td :class="'td '+ n2.bkc||''">{{ n2.load }}</td>
                    </tr>
                  </table>
                </div>
              </div>
            </el-card>
          </el-timeline-item>
        </el-timeline>
      </div>
    </el-card>
    <edit-page ref="editPage" />
    <error-log ref="errorLogPage" />
    <setting-page ref="settingPage" />
  </div>
</template>

<script>
import editPage from './edit'
import errorLog from './errorLog'
import settingPage from './setting'
export default {
  components: { editPage, errorLog, settingPage },
  data() {
    return {
      waterMark: '',
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
      tableList: [
        {
          dateTime: '2023-12-03 10:03:46',
          name: '于丽婷',
          completed: false,
          status: 1,
          expand: false,
          errorNum: 0
        }, {
          dateTime: '2023-12-03 10:03:46',
          name: '于丽婷',
          completed: true,
          expand: true,
          errorNum: 0
        }, {
          dateTime: '2023-12-03 10:03:46',
          name: '于丽婷',
          completed: true,
          expand: false,
          errorNum: 3
        }, {
          dateTime: '2023-12-03 10:03:46',
          name: '于丽婷',
          completed: true,
          expand: false,
          errorNum: 3
        }, {
          dateTime: '2023-12-03 10:03:46',
          name: '于丽婷',
          completed: true,
          expand: false,
          errorNum: 0
        }, {
          dateTime: '2023-12-03 10:03:46',
          name: '于丽婷',
          completed: true,
          expand: false,
          errorNum: 0
        }, {
          dateTime: '2023-12-03 10:03:46',
          name: '于丽婷',
          completed: true,
          expand: false,
          errorNum: 0
        }
      ],
      nodelist0: [
        { name: 'UPS1', heat: 25, inputA: 395.9, inputB: 395.5, inputC: 395.9, outputA: 221.2, outputB: 220.6, outputC: 220.6, loadA: 22.2, loadB: 17.9, loadC: 13.2 },
        { name: 'UPS2', heat: 25, inputA: 395.9, inputB: 395.5, inputC: 395.9, outputA: 221.2, outputB: 220.6, outputC: 220.6, loadA: 22.2, loadB: 17.9, loadC: 13.2 }
      ],
      nodelist1: [
        { org: '环翠分公司', rowspan: 4, name: '中信苑', heat: 23, input: 225, output: 219, load: 14 },
        { org: '环翠分公司', name: '望岛机房', heat: 23, input: 225, output: 219, load: 14 },
        { org: '环翠分公司', name: '威高广场机房', heat: 23, input: 225, output: 219, load: 14 },
        { org: '环翠分公司', name: '戚谷疃机房', heat: 23, input: 225, output: 219, load: 14 },
        { bkc: 'bk', org: '高区分公司', rowspan: 3, name: '高区机房', heat: 23, input: 225, output: 219, load: 14 },
        { bkc: 'bk', org: '高区分公司', name: '高区新机房', heat: 23, input: 225, output: 219, load: 14 },
        { bkc: 'bk', org: '高区分公司', name: '电视台机房', heat: 23, input: 225, output: 219, load: 14 },
        { org: '环翠分公司', rowspan: 2, name: '中信苑', heat: 23, input: 225, output: 219, load: 14 },
        { org: '环翠分公司', name: '望岛机房', heat: 23, input: 225, output: 219, load: 14 }
      ],
      nodelist2: [
        { org: '环翠分公司', rowspan: 4, name: '中信苑', heat: 23, input: 225, output: 219, load: 14 },
        { org: '环翠分公司', name: '望岛机房', heat: 23, input: 225, output: 219, load: 14 },
        { org: '环翠分公司', name: '威高广场机房', heat: 23, input: 225, output: 219, load: 14 },
        { org: '环翠分公司', name: '戚谷疃机房', heat: 23, input: 225, output: 219, load: 14 },
        { bkc: 'bk', org: '高区分公司', rowspan: 3, name: '高区机房', heat: 23, input: 225, output: 219, load: 14 },
        { bkc: 'bk', org: '高区分公司', name: '高区新机房', heat: 23, input: 225, output: 219, load: 14 },
        { bkc: 'bk', org: '高区分公司', name: '电视台机房', heat: 23, input: 225, output: 219, load: 14 },
        { org: '环翠分公司', rowspan: 4, name: '中信苑', heat: 23, input: 225, output: 219, load: 14 },
        { org: '环翠分公司', name: '望岛机房', heat: 23, input: 225, output: 219, load: 14 },
        { org: '环翠分公司', name: '威高广场机房', heat: 23, input: 225, output: 219, load: 14 },
        { org: '环翠分公司', name: '戚谷疃机房', heat: 23, input: 225, output: 219, load: 14 },
        { bkc: 'bk', org: '高区分公司', rowspan: 1, name: '高区机房', heat: 23, input: 225, output: 219, load: 14 },
        { org: '环翠分公司', rowspan: 2, name: '中信苑', heat: 23, input: 225, output: 219, load: 14 },
        { org: '环翠分公司', name: '望岛机房', heat: 23, input: 225, output: 219, load: 14 }
      ]
    }
  },
  mounted() {
    this.$nextTick(() => {
      const end = new Date()
      const start = new Date()
      start.setTime(start.getTime() - 3600 * 1000 * 72)
      this.timeList = [start, end]
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
    })
  },
  methods: {
    expandTable(item) {
      item.expand = !item.expand
    },
    toAdd() {
      this.$refs.editPage.loadData()
    },
    openErrorLog(item) {
      if (item.errorNum) {
        this.$refs.errorLogPage.loadData(item)
      }
    },
    toSetting() {
      this.$refs.settingPage.loadData()
    },
    addInspection(formData) {
      this.tableList.unshift({
        dateTime: formData.dateTime,
        name: formData.inspector,
        completed: false,
        status: 1,
        expand: false,
        errorNum: 0
      })
    },
    beginInspection(item) {
      item.status = 2
      setTimeout(() => {
        item.status = 3
        item.expand = true
      }, 2500)
    },
    submitInspection(item) {
      item.completed = true
      item.expand = false
    },
    loadData() {

    }
  }
}
</script>

<style lang="scss" scoped>
.table-card-box {
  .table-card-title {
    width: 100%;
    padding: 2px;
    margin-bottom: 10px;
    text-align: center;
    font-weight: bold;
    font-size: 18px;
    background-color: rgba(0,0,0,0.1);
  }
  .table {
    border-collapse: collapse;
    width: 100%;
    margin-bottom: 20px;
    text-align: left;
    border-radius: 5px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
  }
  .th, .td {
    border: 1px solid #ddd;
    padding: 8px;
    border-radius: 5px;
    text-align: center;
  }
  .th {
    background-color: #e2e2e2;
    font-weight: bold;
    box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
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
  .signImg {
    position: absolute;
    top: 34px;
    left: 520px;
  }
  .expand-btn {
    position: absolute;
    top: 48px;
    right: 20px;
    font-size: 14px;
    font-weight: bold;
    color: #1961C5;
    opacity: 0.8;
  }

  .expand-btn:hover {
    opacity: 1;
  }
}
</style>
