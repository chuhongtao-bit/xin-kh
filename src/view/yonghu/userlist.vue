<template>
  <div id="app">
    <!--   模糊查询   -->
    <el-form :inline="true" :model="mypage" class="demo-form-inline" style="text-align: center">


      <el-form-item label="用户名">
        <el-input v-model="mypage.name" placeholder="用户名"></el-input>
      </el-form-item>

      <el-form-item label="创建日期">
        <div class="block">

          <el-date-picker
            v-model="mypage.data1"
            value-format="yyyy-MM-dd"
            type="date"
            placeholder="选择日期">
          </el-date-picker>
          <el-date-picker
            v-model="mypage.data2"
            value-format="yyyy-MM-dd"
            type="date"
            placeholder="选择日期">
          </el-date-picker>
        </div>

      </el-form-item>


      <el-form-item label="性别">
        <el-select v-model="mypage.sex" placeholder="性别" clearable>
          <el-option label="男" value="1"></el-option>
          <el-option label="女" value="2"></el-option>
        </el-select>
      </el-form-item>


      <el-form-item>
        <el-button type="primary" @click="getuserList">查询</el-button>
      </el-form-item>


    </el-form>

    <el-button type="primary" @click="dialogFormVisible = true">+添加新用户</el-button>


    <!--添加-->
    <el-dialog title="添加" :visible.sync="dialogFormVisible" :before-close="diaclose">

      <el-form :model="entityUser">


        <el-form :inline="true" :model="entityUser" class="demo-form-inline">
          <el-form-item label="用户名" :label-width="formLabelWidth">
            <el-input v-model="entityUser.userName" autocomplete="off"></el-input>
          </el-form-item>

          <el-form-item label="登录名" :label-width="formLabelWidth">
            <el-input v-model="entityUser.loginName" autocomplete="off"></el-input>
          </el-form-item>
        </el-form>

        <el-form-item label="性别" :label-width="formLabelWidth">
          <el-radio-group v-model="entityUser.sex">
            <el-radio-button label="1">男</el-radio-button>
            <el-radio-button label="2">女</el-radio-button>
          </el-radio-group>
        </el-form-item>


        <el-form-item label="密码" :label-width="formLabelWidth">
          <el-input type="password" v-model="entityUser.password" autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item label="确认密码" :label-width="formLabelWidth">
          <el-input type="password" v-model="entityUser.password1" autocomplete="off"></el-input>
        </el-form-item>


      </el-form>

      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false, entityUser={} ">取 消</el-button>
        <el-button type="primary" @click="addUser(entityUser.id)">确 定</el-button>
      </div>
    </el-dialog>


    <!--  绑定角色  -->
    <div>
      <el-dialog
        title="绑定角色"
        :visible.sync="bindroledia"
        width="30%">
        角色：
        <template>
          <el-select v-model="value" filterable placeholder="请选择">
            <el-option
              v-for="item in options"
              :key="item.id"
              :label="item.roleName"
              :value="item.id">
            </el-option>
          </el-select>
        </template>
        <span slot="footer" class="dialog-footer">
            <el-button @click="diaclose">取 消</el-button>
        <el-button type="primary" @click="addbindRole">确 定</el-button>
        </span>
      </el-dialog>
    </div>


    <!--表单-->
    <el-table
      :data="userList"
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
        label="用户名"
        width="180">
        <template slot-scope="scope">
          <el-popover trigger="hover" placement="top">
            <p>用户名: {{ scope.row.userName }}</p>
            <p>登录名: {{ scope.row.loginName }}</p>
            <p>性别: {{sexdata(scope.row.sex)}}</p>
            <p>电话: {{ scope.row.tel }}</p>
            <p>部门: {{ scope.row.dept }}</p>
            <p>角色: {{ scope.row.roleName }}</p>
            <div slot="reference" class="name-wrapper">
              <el-tag size="medium">{{ scope.row.userName }}</el-tag>
            </div>
          </el-popover>
        </template>
      </el-table-column>

      <el-table-column
        prop="loginName"
        label="登录名"
        sortable
        width="180">
      </el-table-column>

      <el-table-column
        align="center"
        :formatter="formatRole"
        prop="sex"
        label="性别"
        width="100">
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
        prop="roleName"
        label="已绑定角色"
        width="180">
      </el-table-column>

      <el-table-column
        label="创建日期"
        width="120">
        <template slot-scope="scope">{{ scope.row.createTime }}</template>
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
            @click="updateUser(scope.$index, scope.row)">编辑
          </el-button>

          <!--          绑定角色-->
          <el-button size="mini" type="primary" @click="bindRole(scope.$index, scope.row)" v-if="scope.row.id!=pid">
            绑定角色
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
        userList: [],
        total: 0,
        pageSizes: [2, 3, 5, 10],
        pageSize: 5,
        currentPage: 1,
        mypage: {page: "1", pageSize: "5", sex: "", data1: "", data2: "", name: ""},
        multipleSelection: [],
        entityUser: {},
        dialogFormVisible: false,//添加修改弹框
        bindroledia: false,//绑定用户角色弹框
        formLabelWidth: '120px',
        radio: '1',
        options: [],//绑定用户回显的值
        pid: this.$store.state.userInfo.id,
        value: '',//绑定选中角色的id
        userId: "",//添加角色的id
      }
    },
    mounted: function () {
      console.log("进入用户列表");
      this.getuserList();
    },
    methods: {
      getuserList() {
        if (this.mypage.data1 == null) {
          this.mypage.data1 = '';
        }
        if (this.mypage.data2 == null) {
          this.mypage.data2 = '';
        }
        if (this.mypage.sex == null) {
          this.mypage.sex = '';
        }
        if (this.mypage.name == null) {
          this.mypage.name = '';
        }
        this.$axios.post(this.domain.serverpath + 'userList', this.mypage).then((res) => {
          this.userList = res.data.content;
          this.total = res.data.totalElements;
        }).catch()
      },
      nextOrOtherPage(currentPage) {
        this.mypage.pageSize = this.pageSize;
        this.mypage.page = currentPage;
        this.getuserList();
      },
      pageSizeChange(pageSize) {
        this.pageSize = pageSize;
        this.mypage.pageSize = this.pageSize;
        this.getuserList();
      },
      formatRole: function (row, column) {
        return row.sex == '1' ? "男" : row.sex == '2' ? "女" : "数据错误";
      },
      sexdata(sex) {
        return sex == '1' ? "男" : sex == '2' ? "女" : "数据错误";
      },
      deleteById(index, row) {
        alert(JSON.stringify(index));
        alert(JSON.stringify(row.id));
        this.$axios.post(this.domain.serverpath + 'deleteUserById', row).then((res) => {
          console.log(res.data);
          if (res.data == 200) {
            this.$message({
              showClose: true,
              message: "删除成功",
              type: 'success',
              duration: 1000
            });
            this.getuserList();
          }
        }).catch((x) => {
            this.$message({
              message: '未有操作权限',
              type: 'error',
              duration: '1000'
            });
          }
        )
      },
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      addUser(id) {
        if (id > 0) {
          this.$axios.post(this.domain.serverpath + 'updateUser', this.entityUser).then((res) => {
            alert(JSON.stringify(this.entityUser));
            if (res.data == 200) {
              this.$message({
                showClose: true,
                message: "修改成功",
                type: 'success',
                duration: 1000
              });
              this.getuserList();
              this.entityUser = {};
              this.dialogFormVisible = false
            }
          }).catch((x) => {
            this.$message({
              message: '未有操作权限',
              type: 'error',
              duration: '1000'
            });
          })
        } else {
          if (this.entityUser.userName == null) {
            alert("用户名不能为空");
          } else if (this.entityUser.loginName == null) {
            alert("登录名不能为空");
          } else if (this.entityUser.sex == null) {
            alert("请选择性别");
          } else if (this.entityUser.password == null) {
            alert("请输入密码");
          } else if (this.entityUser.password1 == null) {
            alert("请再次输入密码");
          } else if (this.entityUser.password !== this.entityUser.password1) {
            alert("两次密码输入不一致");
          } else {
            this.$axios.post(this.domain.serverpath + 'addUser', this.entityUser).then((res) => {

              if (res.data == 200) {
                this.$message({
                  showClose: true,
                  message: "添加成功",
                  type: 'success',
                  duration: 1000
                });
                this.getuserList();
                this.entityUser = {};
                this.dialogFormVisible = false
              } else if (res.data == 505) {
                alert("登录名重复");
              }

            }).catch((x) => {
                this.$message({
                  message: '未有操作权限',
                  type: 'error',
                  duration: '1000'
                });
              }
            )
          }


        }
      },
      updateUser(index, row) {
        alert(JSON.stringify(row));
        this.entityUser = row;
        this.entityUser.password = "";
        this.dialogFormVisible = true;
      },
      diaclose() {
        this.entityUser = {};
        this.dialogFormVisible = false;
        this.bindroledia = false;
        this.getuserList();
        this.value = '';
      },
      bindRole(index, row) {
        this.userId = row.id;
        this.bindroledia = true;
        this.$axios.post(this.domain.serverpath + "bdrolelist").then((res) => {
          this.options = res.data;
        }).catch((x)=>{
          this.$message({
            message: '未有操作权限',
            type: 'error',
            duration:'1000'
          });
        })
      },
      addbindRole() {
        alert("用户id" + this.userId);
        alert("角色id" + this.value);

        this.$axios.post(this.domain.serverpath + "adduserRole", {
          userId: this.userId,
          roleId: this.value
        }).then((res) => {
          if (res.data == 200) {
            this.$message({
              showClose: true,
              message: "绑定成功",
              type: 'success',
              duration: 1000
            });
            this.getuserList();

            this.bindroledia = false;
          }
        }).catch(
          (x)=>{
            this.$message({
              message: '未有操作权限',
              type: 'error',
              duration:'1000'
            });
          }
        )
        this.value = '';

      }
    }
  }
</script>

<style>

</style>
