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
            <el-button type="primary" size="mini" icon="el-icon-magic-stick" @click="search">查询</el-button>
            <el-button size="mini" icon="el-icon-refresh-left" @click="resetSearch">重置</el-button>
          </div>
          <div class="right-wrapper">
            <el-button type="primary" size="mini" icon="el-icon-plus" @click="toAdd">新增设备</el-button>
          </div>
        </div>
      </el-card>
    </div>
    <el-card :body-style="{padding: 0}" class="table-container" shadow="never">
      <div class="wrapper" :style="{background:waterMark?`url(${waterMark.toDataURL('image/png')}) left top repeat`:``}">
        设备运维表格
      </div>
    </el-card>
  </div>
</template>

<script>
export default {
  components: {},
  data() {
    return {
      waterMark: '',
      query: {
        ip: '',
        port: '',
        address: ''
      },
      tableList: []
    }
  },
  mounted() {
    this.$nextTick(() => {
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
    search() {},
    resetSearch() {},
    loadData() {

    }
  }
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
