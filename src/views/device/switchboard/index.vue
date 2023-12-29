<template>
  <div class="main-container">
    <div class="table-header-container">
      <el-card :body-style="{padding: '0'}" shadow="never">
        <div class="wrapper">
          <div class="left-wrapper">
            <span class="label">数据检索条件:</span>
            <el-input v-model="query.name" clearable size="mini" placeholder="请输入交换机名称" style="width: 150px;margin:0 5px" />
            <el-input v-model="query.ip" clearable size="mini" placeholder="请输入IP地址" style="width: 150px;margin:0 5px" />
            <el-select v-model="query.dw" clearable size="mini" placeholder="请选择所属分管单位" style="width: 160px;margin:0 5px">
              <el-option v-for="item in dw_type" :key="item.value" :label="item.label" :value="item.value" />
            </el-select>
          </div>
          <div class="flex-sub">
            <el-button type="primary" size="mini" icon="el-icon-magic-stick" @click="toSearch">查询</el-button>
            <el-button size="mini" icon="el-icon-refresh-left" @click="clearLimit">重置</el-button>
          </div>
          <div class="right-wrapper">
            <el-button type="primary" size="mini" icon="el-icon-plus" @click="toAdd">新增设备</el-button>
          </div>
        </div>
      </el-card>
    </div>
    <el-card :body-style="{padding: 0}" class="table-container" shadow="never">
      <div class="wrapper" style="overflow: hidden">
        <el-table v-loading="loading" :data="tableData" size="mini" stripe tooltip-effect="dark" height="calc(70vh - 42px)">
          <el-table-column type="index" width="60" label="序号" align="center" :index="indexMethod" />
          <el-table-column align="center" type="expand">
            <template slot-scope="scope">
              <div class="table-card-box">
                <div class="table-card-title">
                  <span><i class="el-icon-plus touch" title="点击添加端口" @click="toPortEdit"></i> 端口巡检列表</span>
                </div>
                <table v-if="scope.row.portList.length" class="table">
                  <tr class="tr">
                    <th class="th" width="25%">端口名称</th>
                    <th class="th" width="35%">对应地址</th>
                    <th class="th" width="10%">通信方式</th>
                    <th class="th" width="10%">接收端OID</th>
                    <th class="th" width="10%">发射端OID</th>
                    <th class="th" width="10%">型号</th>
                  </tr>
                  <tr v-for="(port, ii) in scope.row.portList" :key="'p_'+ii" class="tr">
                    <td>
                      <img :src="require('@/assets/port.png')" style="transform: scaleX(0.8) scaleY(0.8)" />
                      <i class="el-icon-edit-outline touch" title="编辑" @click="toPortEdit(port)"></i>
                      <i class="el-icon-delete touch" title="删除" style="left: 45px" @click="toPortDelete(scope.row, ii)"></i>
                      <span class="touch link-text" title="编辑" @click="toPortEdit(port)">{{ port.name }}</span>
                    </td>
                    <td>{{ port.address }}</td>
                    <td>{{ port.code }}</td>
                    <td>{{ port.code }}</td>
                    <td>{{ port.code }}</td>
                    <td>{{ port.code }}</td>
                  </tr>
                </table>
                <div v-else class="table-card-noData">暂无数据</div>
                <div class="table-card-title" style="margin-top: 4px">
                  <span><i class="el-icon-plus touch" title="点击添加电源" @click="toUpsEdit"></i> 电源设备列表</span>
                </div>
                <table v-if="scope.row.upsList.length" class="table">
                  <tr class="tr">
                    <th class="th" width="25%">电源名称</th>
                    <th class="th" width="35%">电源型号</th>
                    <th class="th">设备OID</th>
                  </tr>
                  <tr v-for="(ups, ii) in scope.row.upsList" :key="'u_'+ii" class="tr">
                    <td>
                      <img :src="require('@/assets/ups.png')" />
                      <i class="el-icon-edit-outline touch" title="编辑" @click="toUpsEdit(ups)"></i>
                      <i class="el-icon-delete touch" title="删除" style="left: 45px" @click="toUpsDelete(scope.row, ii)"></i>
                      <span class="touch link-text" title="编辑" @click="toUpsEdit(ups)">{{ ups.name }}</span>
                    </td>
                    <td>220V交变电源</td>
                    <td>{{ ups.code }}</td>
                  </tr>
                </table>
                <div v-else class="table-card-noData">暂无数据</div>
              </div>
            </template>
          </el-table-column>
          <el-table-column prop="name" label="交换机名称" sortable />
          <el-table-column prop="ip" label="IP地址" align="center" sortable />
          <el-table-column prop="dw" label="所属分管单位" align="center" sortable />
          <el-table-column label="端口数量" align="center" width="100" sortable>
            <template slot-scope="scope">
              {{ scope.row.portList.length || '暂无' }}
            </template>
          </el-table-column>
          <el-table-column label="电源数量" align="center" width="100" sortable>
            <template slot-scope="scope">
              {{ scope.row.upsList.length || '暂无' }}
            </template>
          </el-table-column>
          <el-table-column prop="dataTime" label="最后维护日期" align="center" width="150" sortable />
          <el-table-column label="操作" align="center" width="120">
            <template slot-scope="scope">
              <el-tooltip content="编辑"><el-button round plain type="primary" size="mini" icon="el-icon-edit-outline" @click="toEdit(scope.row)" /></el-tooltip>
              <el-tooltip content="删除"><el-button round plain type="danger" size="mini" icon="el-icon-delete" @click="toDelete(scope.row)" /></el-tooltip>
            </template>
          </el-table-column>
        </el-table>
        <!--分页组件-->
        <el-pagination :total="total" :current-page="page" :page-size="pageSize" style="margin-top: 8px;" layout="total, prev, pager, next, sizes" @size-change="sizeChange" @current-change="pageChange" />
      </div>
    </el-card>
    <edit-page ref="editPage" />
    <port-edit ref="portEdit" />
    <ups-edit ref="upsEdit" />
  </div>
</template>

<script>
import editPage from './edit'
import portEdit from './portEdit'
import upsEdit from './upsEdit'
export default {
  components: { editPage, portEdit, upsEdit },
  data() {
    return {
      waterMark: '',
      dw_type: [
        { label: '市公司', value: '市公司' },
        { label: '环翠分公司', value: '环翠分公司' },
        { label: '高区分公司', value: '高区分公司' },
        { label: '经区分公司', value: '经区分公司' }
      ],
      loading: false,
      page: 1,
      pageSize: 20,
      total: 0,
      query: {
        name: '',
        code: '',
        dw: '',
        ip: ''
      },
      tableData: [
        {
          name: 'IPTV环网-威海7503',
          code: '123456',
          dw: '市公司',
          ip: '10.112.9.214',
          dataTime: '2023-12-21 10:31:58',
          upsList: [
            { name: 'XGE0/0/25-ups', model: '220V-1A交变', code: '123' }
          ],
          portList: [
            { name: 'XGE0/0/25', code: '123', address: '威海6520' },
            { name: 'XGE0/0/25', code: '123', address: '威海6520' },
            { name: 'XGE0/0/25', code: '123', address: '威海6520' },
            { name: 'XGE0/0/25', code: '123', address: '威海6520' }
          ]
        },
        {
          name: 'IPTV环网-威海7503',
          code: '123456',
          dw: '市公司',
          ip: '10.112.9.214',
          dataTime: '2023-12-21 10:31:58',
          upsList: [],
          portList: []
        },
        {
          name: 'IPTV环网-威海7503',
          code: '123456',
          dw: '市公司',
          ip: '10.112.9.214',
          dataTime: '2023-12-21 10:31:58',
          upsList: [
            { name: 'XGE0/0/25-ups', model: '220V-1A交变', code: '123' }
          ],
          portList: [
            { name: 'XGE0/0/25', code: '123', address: '威海6520' },
            { name: 'XGE0/0/25', code: '123', address: '威海6520' },
            { name: 'XGE0/0/25', code: '123', address: '威海6520' },
            { name: 'XGE0/0/25', code: '123', address: '威海6520' }
          ]
        }
      ]
    }
  },
  mounted() {
    this.$nextTick(() => {
    })
  },
  methods: {
    indexMethod(index) {
      return 1 + index + this.page * this.pageSize - this.pageSize
    },
    toAdd() {
      this.$refs.editPage.loadData()
    },
    toEdit(item) {
      this.$refs.editPage.loadData(item)
    },
    toPortEdit(item) {
      this.$refs.portEdit.loadData(item)
    },
    toUpsEdit(item) {
      this.$refs.upsEdit.loadData(item)
    },
    toDelete(item) {
      this.$confirm(`确认删除”${item.name}“交换机吗?`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$successMsg('数据删除成功!')
        this.loadData()
      })
    },
    toPortDelete(item, index) {
      this.$confirm(`确认删除“${item.name}”交换机“${item.portList[index].name}”端口吗?`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        item.portList.splice(index, 1)
        this.$successMsg('行数据删除成功!')
      })
    },
    toUpsDelete(item, index) {
      this.$confirm(`确认删除”${item.name}”交换机“${item.upsList[index].name}”电源吗?`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        item.upsList.splice(index, 1)
        this.$successMsg('行数据删除成功!')
      })
    },
    toSearch() {
      this.page = 1
      this.loadData()
    },
    clearLimit() {
      this.query = {}
      this.loadData()
    },
    pageChange(e) {
      this.page = e
      this.loadData()
    },
    sizeChange(e) {
      this.page = 1
      this.pageSize = e
      this.loadData()
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
    padding: 2px 10px;
    color: #333333;
    font-weight: bold;
    font-size: 14px;
    background-color: rgba(0,0,0,0.15);
    text-align: center;
    span {
      font-size: 15px;
      padding-left: 4px;
    }
    i {
      float: left;
    }
    i:hover {
      color: #3a8ee6;
    }
  }
  .table-card-noData {
    width: 100%;
    text-align: center;
    color: #4e4e4e;
    border: 1px dashed #ddd;
  }
  table {
    margin-top: 1px;
    border-collapse: collapse;
    width: 100%;
    text-align: left;
    border-radius: 5px;
  }
  th, td {
    border: 1px solid #ddd;
    padding: 4px;
    border-radius: 5px;
    text-align: center;
    img {
      position: absolute;
      top: 4px;
      left: 10px;
    }
    i {
      position: absolute;
      top: 6px;
      left: 30px;
    }
    i:hover {
      color: #3a8ee6;
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
