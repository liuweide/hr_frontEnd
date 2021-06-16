<template>
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>请休假管理</el-breadcrumb-item>
      <el-breadcrumb-item>已发送的请假申请</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card>
      <el-row>
        <el-col :span="10">
          <div class="grid-content bg-purple-dark">
          </div>
        </el-col>
      </el-row>
      <el-table :data="holidayList" border style="width: 100%">
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
        </el-table-column>
        <el-table-column
          prop="bdate"
          label="请假开始日期"
          width="180">
        </el-table-column>
        <el-table-column
          prop="edate"
          label="请假结束日期">
        </el-table-column>
        <el-table-column
          prop="approve_result"
          label="审批结果">
        </el-table-column>
        <el-table-column
          prop="state"
          label="流程状态">
        </el-table-column>
      </el-table>
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :page-sizes="page_sizes"
        :page-size="page_size"
        layout="total, sizes, prev, pager, next"
        :total="total">
      </el-pagination>
    </el-card>
  </div>
</template>
<script>
export default {
  data () {
    return {
      userName: '',
      holidayList: [],
      page_sizes: [1, 2, 5, 10],
      page_size: 5,
      current_page: 1,
      total: 0
    }
  },
  created () {
    this.searchHoliday()
  },
  methods: {
    searchHoliday () {
      const currentIndex = (this.current_page - 1) * this.page_size
      this.$http.get('/user/get_user_holidayflow?user_name=' + this.userName + '&current_index=' + currentIndex + '&page_size=' + this.page_size)
        .then(res => {
          if (res.data.state === 200) {
            console.log(res.data)
            this.holidayList = res.data.data
            for (var i = 0; i < this.holidayList.length; i++) {
              if (this.holidayList[i].type === 0) {
                this.holidayList[i].type = '新增'
              } else {
                this.holidayList[i].type = '修改'
              }

              //
              if (this.holidayList[i].approve_result === null) {
                this.holidayList[i].approve_result = ''
              } else if (this.holidayList[i].approve_result === 0) {
                this.holidayList[i].approve_result = '同意'
              } else if (this.holidayList[i].approve_result === 1) {
                this.holidayList[i].approve_result = '不同意'
              }

              //
              if (this.holidayList[i].state === 0) {
                this.holidayList[i].state = '未结束'
              } else if (this.holidayList[i].state === 1) {
                this.holidayList[i].state = '已结束'
              }
            }
            this.total = res.data.total
          } else {
            this.$message.error('获取已发的流程失败')
          }
        })
        .catch(_error => {
          this.$message.error('获取已发的流程失败')
        })
    },
    // 修改page_size
    handleSizeChange (pageSize) {
      this.page_size = pageSize
      this.searchHoliday()
    },
    // 修改current_page
    handleCurrentChange (currentPage) {
      this.current_page = currentPage
      this.searchHoliday()
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
