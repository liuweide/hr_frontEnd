<template>
  <div>
    <!-- 面包屑 -->
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>请休假管理</el-breadcrumb-item>
      <el-breadcrumb-item>全行假期查询</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片 -->
    <el-card>
      <el-row>
        <el-col :span="10">
          <div class="grid-content bg-purple-dark">
            <el-input placeholder="请输入用户名称" v-model="userName" @change="searchUserHoliday()">
              <el-button slot="append" icon="el-icon-search" @click="searchUserHoliday()"></el-button>
            </el-input>
          </div>
        </el-col>
      </el-row>
      <el-table :data="userHolidayList" border style="width: 100%">
        <el-table-column
          prop="user"
          label="用户名"
          width="100">
        </el-table-column>
        <el-table-column
          prop="bdate"
          label="请假开始日期"
          width="180">
        </el-table-column>
        <el-table-column
          prop="edate"
          width="250"
          label="请假结束日期">
        </el-table-column>
        <el-table-column
          prop="apply_date"
          width="180"
          label="申请日期">
        </el-table-column>
        <el-table-column
          prop=""
          label="操作">
          <template slot-scope="scope">
            <el-button type="primary" @click="showModifyDialog(scope.row)" size="mini">修改</el-button>
          </template>
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
    <!-- 修改请假 -->
    <el-dialog
      title="修改请假" :visible.sync="modifyHolidayDialogVisable" width="35%" @close="resetModifyDialog">
      <el-form ref="modifyFormRef" :model="modifyHolidayForm" label-width="110px" :rules="modifyHolidayFormRules">
        <el-form-item label="用户名：" >
          <el-input v-model="modifyHolidayForm.user" disabled></el-input>
        </el-form-item>
        <el-form-item label="请假开始日期：" prop="bdate">
          <el-date-picker value-format="yyyy-MM-dd"
                          v-model="modifyHolidayForm.bdate"  type="date" placeholder="选择日期">
          </el-date-picker>
        </el-form-item>
        <el-form-item label="请假结束日期：" prop="edate">
          <el-date-picker value-format="yyyy-MM-dd"
                          v-model="modifyHolidayForm.edate"  type="date" placeholder="选择日期">
          </el-date-picker>
        </el-form-item>
        <el-form-item label="申请时间：" prop="apply_date">
          <el-date-picker value-format="yyyy-MM-dd"
                          v-model="modifyHolidayForm.apply_date"  type="date" placeholder="选择日期">
          </el-date-picker>
        </el-form-item>
        <el-form-item >
              <span class="button_span">
                <el-button type="primary" @click="modifyHoliday()" size="small">修改</el-button>
                <el-button type="info" @click="resetModifyDialog ()" size="small">重置</el-button>
              </span>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>
<script>
export default {
  data () {
    return {
      userName: '',
      userHolidayList: [],
      page_sizes: [1, 2, 5, 10],
      page_size: 5,
      current_page: 1,
      total: 0,
      modifyHolidayDialogVisable: false,
      modifyHolidayForm: {
        user: '',
        bdate: '',
        edate: '',
        apply_date: ''
      },
      modifyHolidayFormRules: {
        bdate: [{ required: true, message: '开始日期不能为空', trigger: true }],
        edate: [{ required: true, message: '结束日期不能为空', trigger: true }]
      }
    }
  },
  created () {
    this.searchUserHoliday()
  },
  methods: {
    // 搜索所有用户请假
    searchUserHoliday () {
      const currentIndex = (this.current_page - 1) * this.page_size
      this.$http.get('/admin/get_all_holiday?username=' + this.userName + '&current_index=' + currentIndex + '&page_size=' + this.page_size)
        .then(res => {
          if (res.data.state === 200) {
            this.total = res.data.total
            this.userHolidayList = res.data.data
          } else {
            this.$message.error('获取所有人请假失败')
          }
        })
        .catch(_error => {
          this.$message.error('获取所有人请假失败')
        })
    },
    // 修改page_size
    handleSizeChange (pageSize) {
      this.page_size = pageSize
      this.searchUserHoliday()
    },
    // 修改current_page
    handleCurrentChange (currentPage) {
      this.current_page = currentPage
      this.searchUserHoliday()
    },
    // 显示修改对话框
    showModifyDialog (row) {
      this.modifyHolidayDialogVisable = true
      this.modifyHolidayForm = row
      console.log(row)
    },
    // 重置修改对话框
    resetModifyDialog () {
      this.$refs.modifyFormRef.resetFields()
    },
    // 修改
    modifyHoliday () {
      this.$http.post('/admin/modify_holiday', this.modifyHolidayForm)
        .then(res => {
          if (res.data.state === 200) {
            this.modifyHolidayDialogVisable = false
            this.searchUserHoliday()
            this.$message.success('修改请假成功')
          } else {
            this.$message.error('修改请假失败')
          }
        })
        .catch(_error => {
          this.$message.error('修改请假失败')
        })
    }
  }
}
</script>
<style scoped>
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
