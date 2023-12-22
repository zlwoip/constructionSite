<template>
  <!-- 表单渲染 -->
  <el-dialog
    append-to-body
    :close-on-click-modal="false"
    :before-close="cancelView"
    :visible="visible"
    :title="title"
    top="20vh"
    width="960px"
  >
    <div v-for="(iptv, i) in tableList" :key="'iptv_'+i" class="table-card-box" style="margin-bottom: 16px">
      <div class="table-card-title">
        <span>{{ iptv.name }}</span>
        <span style="float: right;color: #999999;font-size: 14px">IP: {{ iptv.ip }} </span>
      </div>
      <div style="padding-top:8px">
        <table class="table">
          <tr class="tr">
            <th class="th">端口</th>
            <th class="th">对应地址</th>
            <th class="th">光接收功率</th>
            <th class="th">光发射功率</th>
          </tr>
          <tr v-for="(port, ii) in iptv.portList" :key="'p_'+i+'_'+ii" class="tr">
            <td>{{ port.port }}</td>
            <td>{{ port.address }}</td>
            <td :class="port.input>-3.5?'warn':''">{{ port.input }}</td>
            <td :class="port.output>-1.69?'warn':''">{{ port.output }}</td>
          </tr>
        </table>
      </div>
    </div>
    <div slot="footer" class="dialog-footer" style="text-align: center">
      <el-button size="mini" icon="el-icon-circle-close" @click="cancelView">关闭</el-button>
    </div>
  </el-dialog>
</template>
<script>

export default {
  data() {
    return {
      visible: false,
      title: '',
      tableList: [
        {
          name: 'IPTV环网-威海7503',
          ip: '10.253.174.240',
          ups: '电源正常',
          errorNum: 0,
          portList: [
            { port: 'XGE0/0/25', address: '威海6520', input: -2.99, output: -0.97 },
            { port: 'XGE2/0/1', address: '乳山6520', input: -5.19, output: -1.66 }
          ]
        },
        {
          name: 'IPTV环网-文登6520',
          ip: '10.253.174.225',
          ups: '暂不支持',
          errorNum: 0,
          portList: [
            { port: 'XGE1/0/24', address: '文登7604', input: -3.47, output: -1.78 }
          ]
        }
      ],
      dataObj: {}
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
    loadData(obj) {
      this.title = `交换机异常巡检日志 - ${obj.dateTime}`
      this.showView()
    }
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
.warn {
  color: red;
}
.box-card {
  display: inline-block;
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
    color: #4e4e4e;
    font-weight: bold;
    font-size: 14px;
    background-color: rgba(0,0,0,0.1);
    span {
      font-size: 16px;
      padding: 0 10px;
    }
  }
  table {
    border-collapse: collapse;
    width: 100%;
    text-align: left;
    border-radius: 5px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
  }
  th, td {
    border: 1px solid #ddd;
    padding: 4px;
    border-radius: 5px;
    text-align: center;
  }
  th {
    background-color: #e2e2e2;
    font-weight: bold;
    box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
  }
  .bk {
    background-color: #f2f2f2;
  }
}
.cell-box {
  min-width: 120px;

  .cell-input {
    width: 220px;
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
