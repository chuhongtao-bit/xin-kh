<template>
  <div id="app">
menulist
    <div>
      <el-input
        placeholder="输入关键字进行过滤"
        v-model="filterText">
      </el-input>

      <el-tree
        class="filter-tree"
        :data="data"
        :props="defaultProps"
        show-checkbox
        default-expand-all
        :filter-node-method="filterNode"
        ref="tree">
      </el-tree>
    </div>

  </div>
</template>

<script>

  export default {
    name: 'menulist',
    data() {
      return {
        filterText: '',
        data: [],//总权限列表
        defaultProps: {
          children: 'menuInfoList',//下级权限列表
          label: 'menuName'//权限名字
        }

      }
    },
    watch: {
      filterText(val) {
        this.$refs.tree.filter(val);
      }
    },
    mounted: function () {
      console.log("进入权限列表");

      //获取所有的权限
      this.$axios.post(this.domain.serverpath+"menuList").then((res)=>{
        this.data=res.data.result;
      })
    },
    methods: {
      filterNode(value, data) {
        if (!value) return true;
        //menuName 过滤方法
        return data.menuName.indexOf(value) !== -1;
      }
    }
  }
</script>

<style>

</style>
