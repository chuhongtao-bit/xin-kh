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


    <!-- 添加  -->
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

    <!--  绑定权限  -->
    <div>
      <el-dialog
        title="编辑角色"
        :visible.sync="bindusermenu"
        width="40%"
        :before-close="diaclose">
        <el-form label-width="100px" ref="entitymod" :model="entityRole">

          <el-form-item label="角色名称">
            <el-input type="text" v-model="entityRole.roleName" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="角色描述">
            <el-input type="text" v-model="entityRole.miaoShu" autocomplete="off"></el-input>
          </el-form-item>

        </el-form>
        权限列表信息
        <el-tree
          :data="menudata"
          show-checkbox
          default-expand-all
          node-key="id"
          ref="tree"
          highlight-current

          :props="defaultProps">
        </el-tree>
        <span slot="footer" class="dialog-footer">
            <el-button @click="diaclose">取 消</el-button>
          <el-button type="primary" @click="getCheckedKeys">确 定</el-button>
        </span>
      </el-dialog>

    </div>


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
            @click="deleteroleById(scope.$index, scope.row)">删除
          </el-button>

          <el-button
            size="mini"
            type="primary"
            @click="updateRole(scope.$index, scope.row)">编辑绑定权限
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
        bindusermenu: false,
        formLabelWidth: '120px',
        menudata: [],//角色权限回显
        defaultProps: {
          children: 'menuInfoList',
          label: 'menuName'
        }
      }
    },
    mounted: function () {
      console.log("进入角色列表");
      this.getroleList();
      //获取角色权限回显
      this.$axios.post(this.domain.serverpath + "findMenu").then((res) => {
        this.menudata = res.data;
      })

    },
    methods: {
      getroleList() {
        if (this.mypage.name == null) {
          this.mypage.name = '';
        }
        this.$axios.post(this.domain.serverpath + 'roleList', this.mypage).then((res) => {
          this.roleList = res.data.content;
          this.total = res.data.totalElements;
        }).catch(

        )
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
      deleteroleById(index, row) {
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
        }).catch(

        )
      },
      addRole(id) {
        if (id > 0) {
          this.$axios.post(this.domain.serverpath + 'updateUser', this.entityRole).then((res) => {
            alert(JSON.stringify(this.entityRole));
            if (res.data == 200) {
              this.$message({
                showClose: true,
                message: "修改成功",
                type: 'success',
                duration: 1000
              });
              this.getuserList();
              this.entityRole = {};
              this.dialogFormVisible = false
            }

          }).catch((x)=>{
            this.$message({
              message: '未有操作权限',
              type: 'error',
              duration:'1000'
            });
          })
        } else {
          this.$axios.post(this.domain.serverpath + 'addRole', this.entityRole).then((res) => {
            if (res.data == 200) {
              this.$message({
                showClose: true,
                message: "添加成功",
                type: 'success',
                duration: 1000
              });
              this.getroleList();
              this.entityRole = {};
              this.dialogFormVisible = false
            }

          }).catch((x)=>{
            this.$message({
              message: '未有操作权限',
              type: 'error',
              duration:'1000'
            });
          })
        }
      },
      diaclose() {
        this.entityRole = {};
        this.dialogFormVisible = false;
        this.bindusermenu = false;
      },//点击角色绑定权限
      updateRole(index, row) {
        this.entityRole = row;
        this.bindusermenu = true;

        setTimeout(()=>{
          this.$refs.tree.setCheckedNodes(row.menuInfoList.filter(i=>i.leval == 4));
        },0)
      },//角色绑定权限
      getCheckedKeys() {
        let k1=this.$refs.tree.getCheckedKeys()
        let k2=this.$refs.tree.getHalfCheckedKeys();
        let k3=[];
        k3=k3.concat(k1).concat(k2);

        this.entityRole.ids=k3;

        this.$axios.post(this.domain.serverpath+"addRm",this.entityRole).then((res)=>{

          if(res.code=200){
            this.$message({
              message: '恭喜你，绑定成功',
              type: 'success',
              duration:'1000'
            });
            this.bindusermenu=false;
            this.getroleList();
          }else{
            this.$message({
              message: '绑定失败',
              type: 'error',
              duration:'1000'
            });
          }
        }).catch((x)=>{
          this.$message({
            message: '你没有操作权限',
            type: 'error',
            duration:'1000'
          });
        })
      }
    }
  }
</script>

<style>

</style>
