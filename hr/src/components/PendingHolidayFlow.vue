<template>
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>请休假管理</el-breadcrumb-item>
      <el-breadcrumb-item>请假待审批流程</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card>
      <el-row>
        <el-col :span="10">
          <div class="grid-content bg-purple-dark">
            <el-col :span="5" class="addBtn">
              <el-button type="info" @click="searchHolidayFlow()">刷新</el-button>
            </el-col>
          </div>
        </el-col>
      </el-row>
      <el-table :data="holidayFlowList" border style="width: 100%">
        <el-table-column
          prop="user_name"
          label="申请人"
          width="100">
        </el-table-column>
        <el-table-column
          prop="apply_date"
          label="申请时间"
          width="100">
        </el-table-column>
        <el-table-column
          prop="type"
          label="申请类型"
          width="100">
          <template slot-scope="scope">
            <el-select v-model="scope.row.type" placeholder="请选择" disabled>
              <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value">
              </el-option>
            </el-select>
          </template>
        </el-table-column>
        <el-table-column
          prop="bdate"
          label="请假开始时间">
        </el-table-column>
        <el-table-column
          prop="edate"
          label="请假结束时间">
        </el-table-column>
        <el-table-column
          prop="date_num"
          label="请假天数">
        </el-table-column>
        <el-table-column
          prop=""
          label="操作" width="180">
          <template slot-scope="scope">
            <el-button type="success" @click="agree(scope.row)" size="mini">同意</el-button>
            <el-button type="info" @click="disagree(scope.row)" size="mini">不同意</el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-card>
  </div>
</template>
<script>
export default {
  data () {
    return {
      holidayFlowList: [],
      value: 0,
      options: [
        {
          value: 0,
          label: '新增'
        },
        {
          value: 1,
          label: '修改'
        }
      ]
    }
  },
  created () {
    this.searchHolidayFlow()
  },
  methods: {
    // 查询待审批的流程
    searchHolidayFlow () {
      this.$http.get('/admin/get_proving_holidayflow?username=&current_index=0&page_size=100')
        .then(res => {
          if (res.data.state === 200) {
            console.log(res.data.data)
            this.holidayFlowList = res.data.data
          } else {
            this.$message.error('获取待审批的流程失败')
          }
        })
        .catch(_error => {
          this.$message.error('获取等审批的流程失败')
        })
    },
    // 同意
    agree (row) {
      this.$http.put('/admin/deal_holiday_flow?id=' + row.id + '&type=0')
        .then(res => {
          if (res.data.state === 200) {
            this.searchHolidayFlow()
            this.$message.success('处理请假流程成功')
          } else {
            this.$message.error('处理请假流程失败1111')
          }
        })
        .catch(_error => {
          this.$message.error('处理请假流程失败' + _error)
        })
    },
    // 不同意
    disagree (row) {
      this.$http.put('/admin/deal_holiday_flow?id=' + row.id + '&type=1')
        .then(res => {
          if (res.data.state === 200) {
            this.searchHolidayFlow()
            this.$message.success('处理请假流程成功')
          } else {
            this.$message.error('处理请假流程失败')
          }
        })
        .catch(_error => {
          this.$message.error('处理请假流程失败' + _error)
        })
    }
  }
}
</script>
<style lang="less" scoped>
.el-table{
  margin-top: 10px;
}
.el-pagination{
  margin-top: 10px;
}
.addBtn{
  margin-left: 10px;
}
</style>
