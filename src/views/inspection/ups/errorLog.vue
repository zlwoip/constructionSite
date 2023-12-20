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
    <div v-if="dataObj.errorNum">
      <div v-if="nodelist0.length" class="table-card-box">
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
          <tr v-for="(n0, i0) in nodelist0" :key="'n_'+'_'+i0" class="tr">
            <td class="td">{{ n0.name }}</td>
            <td class="td">{{ n0.heat }}</td>
            <td class="td">{{ n0.inputA }}</td>
            <td class="td">{{ n0.inputB }}</td>
            <td class="td">{{ n0.inputC }}</td>
            <td class="td">{{ n0.outputA }}</td>
            <td class="td">{{ n0.outputB }}</td>
            <td class="td">{{ n0.outputC }}</td>
            <td class="td">{{ n0.loadA }}</td>
            <td class="td">{{ n0.loadB }}</td>
            <td class="td">{{ n0.loadC }}</td>
          </tr>
        </table>
      </div>
      <div v-if="nodelist1.length" class="table-card-box">
        <div class="table-card-title">一级分前端10KVA-UPS系统</div>
        <table class="table">
          <tr class="tr">
            <th class="th">分公司</th>
            <th class="th">分前端</th>
            <th class="th">温度 (℃)</th>
            <th class="th">输入电压 (V)</th>
            <th class="th">输出电压 (V)</th>
            <th class="th">负载 (%)</th>
            <th class="th">异常原因</th>
          </tr>
          <tr v-for="(n1, i1) in nodelist1" :key="'n_'+'_'+i1" class="tr">
            <td class="td">{{ n1.org }}</td>
            <td class="td">{{ n1.name }}</td>
            <td :class="n1.heat>50?'td warn':'td'">{{ n1.heat }}</td>
            <td :class="n1.input<225?'td warn':'td'">{{ n1.input }}</td>
            <td :class="n1.output<50?'td warn':'td'">{{ n1.output }}</td>
            <td :class="n1.load>50?'td warn':'td'">{{ n1.load }}</td>
            <td class="td">{{ n1.remarks }}</td>
          </tr>
        </table>
      </div>
      <div v-if="nodelist2.length" class="table-card-box">
        <div class="table-card-title">二级分前端及乡镇广播站10KVA-UPS系统</div>
        <table class="table">
          <tr class="tr">
            <th class="th">分公司</th>
            <th class="th">分前端</th>
            <th class="th">温度 (℃)</th>
            <th class="th">输入电压 (V)</th>
            <th class="th">输出电压 (V)</th>
            <th class="th">负载 (%)</th>
            <th class="th">异常原因</th>
          </tr>
          <tr v-for="(n2, i2) in nodelist2" :key="'n_'+'_'+i2" class="tr">
            <td class="td">{{ n2.org }}</td>
            <td class="td">{{ n2.name }}</td>
            <td :class="n2.heat>50?'td warn':'td'">{{ n2.heat }}</td>
            <td :class="n2.input<225?'td warn':'td'">{{ n2.input }}</td>
            <td :class="n2.output<50?'td warn':'td'">{{ n2.output }}</td>
            <td :class="n2.load>50?'td warn':'td'">{{ n2.load }}</td>
            <td class="td">{{ n2.remarks }}</td>
          </tr>
        </table>
      </div>
    </div>
    <div v-else style="text-align: center;font-size: 20px;font-weight: bold">
      一切正常 <i class="el-icon-success" style="color:#00CD66"></i>
    </div>
    <div class="signArea">
      <span>巡检员签字：</span>
      <img class="signImg" :src="require('@/assets/signImg.png')" />
      <img v-show="dataObj.dispose" class="signImg" :src="require('@/assets/dispose.png')" height="80px" style="left:440px;transform:rotate(-30deg)" />
    </div>
    <div slot="footer" class="dialog-footer" style="text-align: center">
      <el-button size="mini" icon="el-icon-circle-close" @click="cancelView">关闭</el-button>
      <el-button v-if="!dataObj.dispose" type="danger" size="mini" icon="el-icon-s-check" @click="submit()">我已处理</el-button>
    </div>
  </el-dialog>
</template>
<script>

export default {
  data() {
    return {
      visible: false,
      title: '',
      nodelist0: [],
      nodelist1: [
        { org: '环翠分公司', name: '中信苑', heat: 66, input: 225, output: 219, load: 14, remarks: '温度过高' },
        { org: '环翠分公司', name: '望岛机房', heat: 23, input: 225, output: 219, load: 88, remarks: '负载过高' }
      ],
      nodelist2: [
        { org: '高区分公司', name: '电视台机房', heat: 23, input: 220, output: 207, load: 14, remarks: '输入电压不稳' }
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
    submit() {
      this.dataObj.dispose = true
    },
    loadData(obj) {
      this.dataObj = obj
      this.title = `UPS异常巡检日志 - ${obj.dateTime}`
      this.showView()
    }
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
.warn {
  color: red;
}
.signArea {
  margin-top: -20px;
  padding: 0 80px;
  font-size: 16px;
  font-weight: bold;
  .signImg {
    position: relative;
    top: 30px;
    left: 10px;
    height: 75px;
  }
}
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
    background-color: #f2f2f2;
    font-weight: bold;
    box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
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
