<template>
  <!-- 表单渲染 -->
  <el-dialog
    append-to-body
    :close-on-click-modal="false"
    :before-close="cancelView"
    :visible="visible"
    :title="title"
    top="25vh"
    width="590px"
  >
    <el-form ref="formViewRef" :model="formData" :rules="rules" size="mini" :status-icon="true" label-width="180px">
      <el-form-item label="光接收功率：" class="form-cell" style="margin: 0;padding: 0">
        <div class="cell-box" style="display: flex">
          <el-form-item prop="minInputTPE">
            <el-input v-model.number="formData.minInputOP" size="mini" placeholder="阈值下限" class="cell-input">
              <template slot="append">下限 dBm</template>
            </el-input>
          </el-form-item>
          <div style="width: 20px;text-align: center">~</div>
          <el-form-item prop="maxInputTPE">
            <el-input v-model.number="formData.maxInputOP" size="mini" placeholder="阈值上限" class="cell-input">
              <template slot="append">上限 dBm</template>
            </el-input>
          </el-form-item>
        </div>
      </el-form-item>
      <el-form-item label="光发射功率：" class="form-cell" style="margin: 0;padding: 0">
        <div class="cell-box" style="display: flex">
          <el-form-item prop="minOutputTPE">
            <el-input v-model.number="formData.minOutputOP" size="mini" placeholder="阈值下限" class="cell-input">
              <template slot="append">下限 dBm</template>
            </el-input>
          </el-form-item>
          <div style="width: 20px;text-align: center">~</div>
          <el-form-item prop="maxOutputTPE">
            <el-input v-model.number="formData.maxOutputOP" size="mini" placeholder="阈值上限" class="cell-input">
              <template slot="append">上限 dBm</template>
            </el-input>
          </el-form-item>
        </div>
      </el-form-item>

    </el-form>
    <div slot="footer" class="dialog-footer" style="text-align: center">
      <el-button size="mini" icon="el-icon-circle-close" @click="cancelView">关闭</el-button>
      <el-button type="primary" size="mini" icon="el-icon-upload2" @click="submitForm()">保存</el-button>
    </div>
  </el-dialog>
</template>
<script>

export default {
  data() {
    return {
      visible: false,
      title: '监听设置',
      formData: {
        minInputOP: -999,
        maxInputOP: -3.5,
        minOutputOP: -999,
        maxOutputOP: -1.69
      },
      rules: {

      }
    }
  },
  mounted() {
  },
  methods: {
    showView() {
      this.visible = true
    },
    hideView() {
      this.$refs.formViewRef.resetFields()
      this.visible = false
    },
    cancelView() {
      this.hideView()
    },
    submitForm() {
      this.$refs.formViewRef.validate((valid, obj) => {
        if (valid) {
          console.log(this.formData)
        } else {
          this.$message({
            message: '表单信息有误，请核对无误后提交！',
            type: 'error'
          })
        }
      })
    },
    loadData() {
      this.showView()
    }
  }
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
.cell-box {
  min-width: 120px;

  .cell-input {
    width: 150px;
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
