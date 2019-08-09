<template>
  <div id="app">
    <!--   模糊查询   -->
    <el-form :inline="true" :model="mypage" class="demo-form-inline" style="text-align: center">


      <el-form-item label="角色名">
        <el-input v-model="mypage.name" placeholder="角色名"></el-input>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="getroleList">查询</el-button>
      </el-form-item>


    </el-form>

    <el-button type="primary" @click="dialogFormVisible = true">+添加新角色</el-button>


    <!--todo 添加  -->
    <el-dialog title="添加角色" :visible.sync="dialogFormVisible" :before-close="diaclose">

      <el-form :model="entityRole">


        <el-form-item label="角色名称" :label-width="formLabelWidth">
          <el-input v-model="entityRole.roleName" autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item label="角色描述" :label-width="formLabelWidth">
          <el-input type="textarea" v-model="entityRole.miaoShu" autocomplete="off"></el-input>
        </el-form-item>


      </el-form>

      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false, entityRole={} ">取 消</el-button>
        <el-button type="primary" @click="addRole(entityRole.id)">确 定</el-button>
      </div>
    </el-dialog>


    <!--表单-->
    <el-table
      :data="roleList"
      style="width: 100%"
      :default-sort="{prop: 'id', order: 'ascending'}"

      ref="multipleTable"
      tooltip-effect="dark"
      @selection-change="handleSelectionChange"
    >
      <el-table-column
        type="selection"
        width="55">
      </el-table-column>

      <el-table-column
        prop="id"
        label="序号"
        sortable
        width="180">
      </el-table-column>

      <el-table-column
        label="角色名称"
        width="180">
        <template slot-scope="scope">
          <el-popover trigger="hover" placement="top">
            <p>序号: {{ scope.row.id }}</p>
            <p>角色名称: {{ scope.row.roleName }}</p>
            <p>描述: {{ scope.row.miaoShu }}</p>
            <div slot="reference" class="name-wrapper">
              <el-tag size="medium">{{ scope.row.roleName }}</el-tag>
            </div>
          </el-popover>
        </template>
      </el-table-column>

      <el-table-column
        prop="miaoShu"
        label="描述"
        width="180">
      </el-table-column>


      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button
            size="mini"
            type="danger"
            @click="deleteById(scope.$index, scope.row)">删除
          </el-button>
          <el-button
            size="mini"
            type="primary"
            @click="updateUser(scope.$index, scope.row)">编辑绑定权限
          </el-button>

        </template>
      </el-table-column>
    </el-table>

    <!--    分页-->
    <el-pagination style="text-align: center"
                   background
                   layout="prev,pager,next,sizes"
                   :total=total
                   :page-sizes=pageSizes
                   :page-size=pageSize
                   :current-page=currentPage
                   @current-change="nextOrOtherPage"
                   @size-change="pageSizeChange"
    >
    </el-pagination>

  </div>
</template>

<script>

  export default {
    name: 'userlist',
    data() {
      return {
        roleList: [],
        total: 0,
        pageSizes: [2, 3, 5, 10],
        pageSize: 5,
        currentPage: 1,
        mypage: {page: "1", pageSize: "5", name: ""},
        multipleSelection: [],
        entityRole: {},
        dialogFormVisible: false,
        formLabelWidth: '120px'
      }
    },
    mounted: function () {
      console.log("vue开始了");
      this.getroleList();
    },
    methods: {
      getroleList() {
        this.$axios.post(this.domain.serverpath + 'roleList', this.mypage).then((res) => {
          this.roleList = res.data.list;
          this.total = res.data.total;
          this.pageSize = res.data.pageSize;
        }).catch()
      },
      nextOrOtherPage(currentPage) {
        this.mypage.pageSize = this.pageSize;
        this.mypage.page = currentPage;
        this.getroleList();
      },
      pageSizeChange(pageSize) {
        this.pageSize = pageSize;
        this.mypage.pageSize = this.pageSize;
        this.getroleList();
      },
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      deleteById(index, row) {
        this.$axios.post(this.domain.serverpath + 'deleteRoleById', row).then((res) => {
          if (res.data == 200) {
            this.$message({
              showClose: true,
              message: "删除成功",
              type: 'success',
              duration: 1000
            });
            this.getroleList();
          }
        }).catch()
      },
      addRole(id){
        if(id>0){
          this.$axios.post(this.domain.serverpath + 'updateUser', this.entityRole).then((res) => {
            alert(JSON.stringify(this.entityRole));
            if (res.data==200){
              this.$message({
                showClose: true,
                message: "修改成功",
                type: 'success',
                duration:1000
              });
              this.getuserList();
              this.entityRole={};
              this.dialogFormVisible = false
            }

          })
        }else {
          this.$axios.post(this.domain.serverpath + 'addRole', this.entityRole).then((res) => {
            if (res.data==200){
              this.$message({
                showClose: true,
                message: "添加成功",
                type: 'success',
                duration:1000
              });
              this.getroleList();
              this.entityRole={};
              this.dialogFormVisible = false
            }

          })
        }
      },
      diaclose(){
        this.entityRole={};
        this.dialogFormVisible=false;
        this.getroleList();
      }
    }
  }
</script>

<style>

</style>
