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
            <el-card>
              <h4>{{ item.name }} </h4>
              <p>王小虎 提交于 2018/4/12 20:46</p>
            </el-card>
          </el-timeline-item>
          <el-timeline-item timestamp="2018/4/3" placement="top">
            <el-card>
              <h4>更新 Github 模板</h4>
              <p>王小虎 提交于 2018/4/3 20:46</p>
            </el-card>
          </el-timeline-item>
          <el-timeline-item timestamp="2018/4/2" placement="top">
            <el-card>
              <h4>更新 Github 模板</h4>
              <p>王小虎 提交于 2018/4/2 20:46</p>
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
          errorNum: 6
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
