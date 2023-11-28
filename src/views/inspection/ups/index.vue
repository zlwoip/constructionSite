<template>
  <div class="main-container">
    <div class="table-header-container">
      <el-card :body-style="{padding: '0'}" shadow="never">
        <div class="wrapper">
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
            <el-button type="primary" size="mini" icon="el-icon-plus">新增巡检</el-button>
            <el-button type="success" size="mini" icon="el-icon-view">查看详情</el-button>
          </div>
        </div>
      </el-card>
    </div>
    <el-card :body-style="{padding: 0}" class="table-container" shadow="never">
      <div class="wrapper">
        <el-timeline>
          <el-timeline-item v-for="(item,index) in tableList" :key="'tm_'+index" :timestamp="item.dateTime" placement="top">
            <el-card v-if="item.completed">
              <div style="font-size: 16px;color: #999999">
                <span style="font-size: 20px;font-weight: bold;color: black">{{ item.name }}</span>
                完成巡检
                <i v-if="item.errorNum===0" class="el-icon-success" style="color:#00CD66"></i>
                <i v-if="item.errorNum>0" class="el-icon-warning" style="color:#FF8C00"></i>
              </div>
              <div class="expand-btn touch">
                展开 >>
              </div>
              <p>本次巡检共发现
                <span :class="item.errorNum?'underline touch':'underline'" :title="item.errorNum?'点击查看异常清单':''" :style="{fontSize:'22px',fontWeight:'bold',color: item.errorNum?'red':'#00CD66'}">{{ item.errorNum }}</span>
                个异常
              </p>
              <div>
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
                  <tr class="tr">
                    <td class="td">UPS1</td>
                    <td class="td">25</td>
                    <td class="td">395.9</td>
                    <td class="td">395.5</td>
                    <td class="td">395.9</td>
                    <td class="td">221.2</td>
                    <td class="td">220.6</td>
                    <td class="td">220.6</td>
                    <td class="td">22.2</td>
                    <td class="td">17.9</td>
                    <td class="td">13.2</td>
                  </tr>
                  <tr class="tr">
                    <td class="td">UPS2</td>
                    <td class="td">25</td>
                    <td class="td">395.9</td>
                    <td class="td">395.5</td>
                    <td class="td">395.9</td>
                    <td class="td">221.2</td>
                    <td class="td">220.6</td>
                    <td class="td">220.6</td>
                    <td class="td">22.2</td>
                    <td class="td">17.9</td>
                    <td class="td">13.2</td>
                  </tr>
                </table>
              </div>
            </el-card>
            <el-card v-else>
              <div style="font-size: 16px;color: #999999">
                <span style="font-size: 20px;font-weight: bold;color: black">{{ item.name }}</span>
                开始巡检...
              </div>
            </el-card>
          </el-timeline-item>
        </el-timeline>
      </div>
    </el-card>
  </div>
</template>

<script>

export default {
  components: {},
  data() {
    return {
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
          name: '张某某',
          completed: false,
          errorNum: 0
        }, {
          dateTime: '2023-12-03 10:03:46',
          name: '张某某',
          completed: true,
          errorNum: 0
        }, {
          dateTime: '2023-12-03 10:03:46',
          name: '张某某',
          completed: true,
          errorNum: 2
        }, {
          dateTime: '2023-12-03 10:03:46',
          name: '张某某',
          completed: true,
          errorNum: 6
        }, {
          dateTime: '2023-12-03 10:03:46',
          name: '张某某',
          completed: true,
          errorNum: 6
        }
      ]
    }
  },
  mounted() {
    // const end = new Date()
    // const start = new Date()
    // start.setTime(start.getTime() - 3600 * 1000 * 24)
    // this.timeList = [start, end]
  },
  methods: {}
}
</script>

<style lang="scss" scoped>
.table {
  border-collapse: collapse;
  width: 100%;
  margin-bottom: 20px;
  text-align: left;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0,0,0,0.3);
}
.th, .td {
  border: 1px solid #ddd;
  padding: 8px;
  border-radius: 5px;
}
.th {
  background-color: #f2f2f2;
  font-weight: bold;
  box-shadow: 0 0 5px rgba(0,0,0,0.2);
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
