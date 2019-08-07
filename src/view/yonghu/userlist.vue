<template>
  <div id="app">

    <!--表单-->
    <el-table
      :data="userList"
      style="width: 100%"
      :default-sort="{prop: 'loginName', order: 'descending'}"
    >
      <el-table-column
        prop="userName"
        label="用户名"
        width="180">
      </el-table-column>

      <el-table-column
        prop="loginName"
        label="登录名"
        sortable
        width="180">
      </el-table-column>

      <el-table-column
        prop="sex"
        label="性别"
        width="180">
      </el-table-column>

      <el-table-column
        prop="tel"
        label="电话"
        width="180">
      </el-table-column>

      <el-table-column
        prop="dept"
        label="部门"
        width="180">
      </el-table-column>

      <el-table-column
        prop="imgulr"
        label="图片路径"
        width="180">
      </el-table-column>

      <el-table-column
        prop=""
        label="已绑定角色"
        width="180">
      </el-table-column>

      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button
            size="mini"
            type="danger"
            @click="handleDelete(scope.$index, scope.row)">删除
          </el-button>
          <el-button
            size="mini"
            @click="handleEdit(scope.$index, scope.row)">编辑
          </el-button>
          <el-button
            size="mini"
            type="primary"
            @click="handleDelete(scope.$index, scope.row)">绑定角色
          </el-button>
        </template>
      </el-table-column>
    </el-table>


  </div>
</template>

<script>

  export default {
    name: 'userlist',
    data() {
      return {
        userList: []

      }
    },
    mounted: function () {
      console.log("vue开始了");
      this.getuserList();
    },
    methods: {
      getuserList() {
        this.$axios.post(this.domain.ssoserverpath + 'userList').then((res) => {
          this.userList = res.data;
        }).catch()
      }
    }
  }
</script>

<style>

</style>
