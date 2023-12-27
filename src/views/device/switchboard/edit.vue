<template>
  <!-- 表单渲染 -->
  <el-dialog append-to-body :close-on-click-modal="false" :before-close="cancelView" :visible="visible" :title="title" width="680px">
    <el-form ref="formViewRef" :model="formData" :rules="rules" :status-icon="true" label-width="200px">
      <el-form-item label="电源名称：" class="form-cell" prop="name">
        <div class="cell-box">
          <el-input v-model="formData.name" size="mini" placeholder="单行文本输入" class="cell-input" />
        </div>
      </el-form-item>
      <el-form-item label="设备编号：" class="form-cell" prop="code">
        <div class="cell-box">
          <el-input v-model="formData.code" size="mini" placeholder="单行文本输入" class="cell-input" />
        </div>
      </el-form-item>
      <el-form-item label="所属分管单位：" class="form-cell" prop="dw">
        <div class="cell-box">
          <el-select v-model="formData.dw" size="mini" placeholder="请选择所属分管单位" class="cell-select">
            <el-option v-for="item in dw_type" :key="item.value" :label="item.label" :value="item.value" />
          </el-select>
        </div>
      </el-form-item>
      <el-form-item label="IP地址：" class="form-cell" prop="ip">
        <div class="cell-box">
          <el-input v-model="formData.ip" size="mini" placeholder="单行文本输入" class="cell-input" />
        </div>
      </el-form-item>
      <el-form-item label="端口巡检列表：" class="form-cell">
        <div v-for="(item, index) in formData.portList" :key="index" class="cell-box" style="margin-bottom: 2px">
          <el-input v-model="item.name" size="mini" placeholder="请输入端口名称" class="cell-input" style="width: 120px" />
          <el-input v-model="item.address" size="mini" placeholder="请输入端口地址" class="cell-input" style="width: 120px;margin: 0 5px" />
          <el-input v-model="item.code" size="mini" placeholder="请输入编码" class="cell-input" style="width: 120px" />
          <el-tooltip v-if="index+1>=formData.portList.length" content="添加">
            <el-button circle size="mini" icon="el-icon-plus" style="margin: 0 5px" @click="addDataRowPort" />
          </el-tooltip>
          <el-tooltip v-else content="删除">
            <el-button circle icon="el-icon-delete" size="mini" style="margin: 0 5px" @click="delDataRow(index, 'portList')" />
          </el-tooltip>
        </div>
      </el-form-item>
      <el-form-item label="电源设备列表：" class="form-cell">
        <div v-for="(item, index) in formData.upsList" :key="index" class="cell-box" style="margin-bottom: 2px">
          <el-input v-model="item.name" size="mini" placeholder="请输入电源名称" class="cell-input" style="width: 120px" />
          <el-input v-model="item.model" size="mini" placeholder="请输入电源型号" class="cell-input" style="width: 120px;margin: 0 5px" />
          <el-input v-model="item.code" size="mini" placeholder="请输入编码" class="cell-input" style="width: 120px" />
          <el-tooltip v-if="index+1>=formData.upsList.length" content="添加">
            <el-button circle size="mini" icon="el-icon-plus" style="margin: 0 5px" @click="addDataRowUps" />
          </el-tooltip>
          <el-tooltip v-else content="删除">
            <el-button circle icon="el-icon-delete" size="mini" style="margin: 0 5px" @click="delDataRow(index, 'upsList')" />
          </el-tooltip>
        </div>
      </el-form-item>
    </el-form>
    <div slot="footer" class="dialog-footer" style="text-align: center">
      <el-button size="mini" @click="cancelView">取消</el-button>
      <el-button size="mini" type="primary" @click="submitForm()">保存</el-button>
    </div>
  </el-dialog>
</template>
<script>

export default {
  data() {
    return {
      visible: false,
      dw_type: [
        { label: '市公司', value: '市公司' },
        { label: '环翠分公司', value: '环翠分公司' },
        { label: '高区分公司', value: '高区分公司' },
        { label: '经区分公司', value: '经区分公司' },
        { label: '荣成分公司', value: '荣成分公司' },
        { label: '文登分公司', value: '文登分公司' },
        { label: '乳山分公司', value: '乳山分公司' }
      ],
      title: 'ups设备信息录入',
      formData: {
        name: '',
        code: '',
        dw: '',
        ip: '',
        upsList: [],
        portList: []
      },
      rules: {
        name: { required: true, message: '请填写电源名称', trigger: 'blur' },
        dw: { required: true, message: '请选择所属分管单位', trigger: 'blur' },
        ip: { required: true, message: '请填写ip地址', trigger: 'blur' }
      }
    }
  },
  mounted() {
  },
  methods: {
    delDataRow(index, keyList) {
      const arrList = this.formData[keyList]
      if (arrList.length > 1) {
        arrList.splice(index, 1)
        this.$successMsg('行数据删除成功!')
      }
    },
    addDataRowPort() {
      const arrList = this.formData.portList
      const rowObj = arrList.pop()
      for (const key in rowObj) {
        if (rowObj[key] === '') {
          this.$errorMsg('信息不完整，无法添加该行数据！')
          return arrList.push(rowObj)
        }
      }
      arrList.push(rowObj)
      arrList.push({ name: '', address: '', code: '' })
    },
    addDataRowUps() {
      const arrList = this.formData.upsList
      const rowObj = arrList.pop()
      for (const key in rowObj) {
        if (rowObj[key] === '') {
          this.$errorMsg('信息不完整，无法添加该行数据！')
          return arrList.push(rowObj)
        }
      }
      arrList.push(rowObj)
      arrList.push({ name: '', model: '', code: '' })
    },
    showView() {
      this.visible = true
    },
    hideView() {
      this.$refs.formViewRef.resetFields()
      this.visible = false
    },
    cancelView() {
      this.formData.portList.pop()
      this.formData.upsList.pop()
      this.hideView()
    },
    submitForm(isRelease) {
      this.$refs.formViewRef.validate((valid, obj) => {
        if (valid) {
          this.cancelView()
        } else {
          this.$message({
            message: '表单信息有误，请核对无误后提交！',
            type: 'error'
          })
        }
      })
    },
    loadData(item) {
      if (item) {
        this.formData = item
      } else {
        this.formData = {
          name: '',
          code: '',
          dw: '',
          ip: '',
          upsList: [],
          portList: []
        }
      }
      this.formData.portList.push({ name: '', code: '', address: '' })
      this.formData.upsList.push({ name: '', code: '', model: '' })
      this.showView()
    }
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
.cell-box {
  min-width: 120px;
  .cell-input {
    width: 220px;
  }
  .cell-select {
    width: 220px;
  }
  >>>.el-input__inner {
    border: 1px solid rgba(100, 100, 100, 0.1);
    border-bottom: 1px solid rgba(100, 100, 100, 0.2);
    border-radius: 5px;
  }
  >>>.el-input.is-disabled .el-input__inner {
    border-radius: 0;
    border: 0;
    border-bottom: 1px solid rgba(100, 100, 100, 0.4);
    background: white;
    cursor: text;
  }
  >>>.el-input.is-disabled .el-input__icon {
    cursor: text;
  }
  >>>.el-icon-circle-check {
    color: #13ce66;
  }
  >>>.el-icon-arrow-up:before {
    content: '';
  }
}
</style>
