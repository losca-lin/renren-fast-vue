<template>
  <el-tree
    :data="data"
    :props="defaultProps"
    :expand-on-click-node="false"
    show-checkbox
    node-key="catId"
    :default-expanded-keys="expandedKey"
  >
    <span class="custom-tree-node" slot-scope="{ node, data }">
        <span>{{ node.label }}</span>
        <span>
          <el-button
            v-if="data.catLevel<=2"
            type="text"
            size="mini"
            @click="() => append(data)">
            Append
          </el-button>
          <el-button
            type="text"
            size="mini"
            v-if="data.children.length == 0"
            @click="() => remove(node, data)">
            Delete
          </el-button>
        </span>
      </span>
  </el-tree>
</template>
<script>
export default {
  data() {
    return {
      data: [],
      defaultProps: {
        children: "children",
        label: "name",
      },
      expandedKey: []
    };
  },
  created() {
    this.getData();
  },
  methods: {
    getData() {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/product/category/list/tree'),
        method: 'get'
      }).then(({data}) => {
        this.data = data.categoryList;
        console.log(data)
      })
    },
    append(data) {
      console.log(data)
    },
    remove(node, data) {
      console.log(node)
      let catIds = [data.catId];
      this.$http({
        url: this.$http.adornUrl('/product/category/delete'),
        method: 'post',
        data: this.$http.adornData(catIds, false)
      }).then(({data}) => {
        this.$confirm(`确定删除菜单【${node.label}】?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除菜单成功!'
          });
          // 刷新新的菜单
          this.getData();
          this.expandedKey = [node.parent.data.catId];
        }).catch(() => {

        })
      })
    }
  },
};
</script>
