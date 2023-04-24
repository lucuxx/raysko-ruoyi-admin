<template>
  <div class="app-container">
    <el-form
      :model="queryParams"
      ref="queryForm"
      size="small"
      :inline="true"
      v-show="showSearch"
      @submit.native.prevent
    >
      <el-form-item label="" prop="cardName">
        <el-input
          v-model="queryParams.cardName"
          placeholder="请输入用户名称"
          clearable
          @keyup.enter.native="handleQuery"
        />
      </el-form-item>

      <el-form-item>
        <el-button
          type="primary"
          icon="el-icon-search"
          size="mini"
          @click="handleQuery"
          >搜索</el-button
        >
        <el-button icon="el-icon-refresh" size="mini" @click="resetQuery"
          >重置</el-button
        >
      </el-form-item>
    </el-form>

    <el-row :gutter="10" class="mb8">
      <el-col :span="1.5">
        <el-button
          type="primary"
          plain
          icon="el-icon-plus"
          size="mini"
          @click="handleAdd"
          >新增</el-button
        >
      </el-col>

      <el-col :span="1.5">
        <el-button
          type="warning"
          plain
          icon="el-icon-download"
          size="mini"
          @click="handleExport"
          >导出</el-button
        >
      </el-col>

      <right-toolbar
        :showSearch.sync="showSearch"
        @queryTable="getList"
      ></right-toolbar>
    </el-row>

    <el-table v-if="refreshTable" v-loading="loading" :data="datalist">
      <template #empty>
        <el-empty></el-empty>
      </template>
      <el-table-column prop="cardId" label="名片ID" min-width="80">
      </el-table-column>
      <el-table-column
        prop="cardName"
        label="用户名称"
        show-overflow-tooltip
      >
        <template #default="{ row }">
          <el-link type="primary" :underline="false" @click="handleLook(row)">
            {{ row.cardName }}
          </el-link>
        </template>
      </el-table-column>
      <el-table-column prop="" label="职务" show-overflow-tooltip></el-table-column>
      <el-table-column prop="" label="联系电话" show-overflow-tooltip></el-table-column>
      <el-table-column prop="" label="邮箱" show-overflow-tooltip></el-table-column>
      <el-table-column prop="" label="微信号" show-overflow-tooltip></el-table-column>
      <el-table-column prop="" label="公司地址" show-overflow-tooltip></el-table-column>
      <el-table-column prop="" label="二维码" show-overflow-tooltip></el-table-column>
      <el-table-column prop="remark" label="备注" show-overflow-tooltip></el-table-column>

      <el-table-column
        label="操作"
        align="center"
        class-name="small-padding fixed-width"
        fixed="right"
        width="200"
      >
        <template #default="{ row }">
          <el-button
            size="mini"
            type="text"
            icon="el-icon-edit"
            @click="handleEdit(row)"
            >编辑</el-button
          >
          <el-button
            size="mini"
            type="text"
            icon="el-icon-delete"
            @click="handleDelete(row)"
            >删除</el-button
          >
        </template>
      </el-table-column>
    </el-table>

    <pagination
      v-show="total > 0"
      :total="total"
      :page.sync="queryParams.pageNum"
      :limit.sync="queryParams.pageSize"
      @pagination="getList"
    />

    <!-- 侧边栏 -->
    <el-drawer
      size="40%"
      :visible.sync="drawer"
      :before-close="handleClose"
      destroy-on-close
      custom-class="drawer-style"
    >
      <template #title>
        <div class="drawer-style__header">
          {{
            mode === "EDIT"
              ? "编辑名片"
              : mode === "LOOK"
              ? "查看名片"
              : "新建名片"
          }}
        </div>
      </template>
      <div class="drawer-style__content">
        <card-detail
          v-if="drawer"
          :cardInfo="cardInfo"
          :mode="mode"
          @drawerClose="drawerClose"
        ></card-detail>
      </div>
    </el-drawer>
  </div>
</template>

<script>
// import { listCategory, delCategory, getCategory } from "@/api/product/category";
import CardDetail from "./components/CardDetail.vue";

export default {
  name: "Category",
  components: { CardDetail },
  data() {
    return {
      // 遮罩层
      loading: false,
      // 显示搜索条件
      showSearch: true,
      // 条数
      total: 0,
      // 产品数据
      datalist: [],
      // 弹出层标题
      title: "",
      // 是否显示弹出层
      drawer: false,
      // 弹框模式
      mode: "ADD",
      // 是否展开，默认全部折叠
      isExpandAll: false,
      // 重新渲染表格状态
      refreshTable: true,
      // 查询参数
      queryParams: {
        pageSize: 10,
        pageNum: 1,
        cardName: "",
      },
      // 表单参数
      cardInfo: {},
    };
  },
  created() {
    // this.getList();
  },
  methods: {
    async getList() {
      // try {
      //   this.loading = true;
      //   const { code, rows, total } = await listCategory(this.queryParams);
      //   if (code === 200) {
      //     this.datalist = rows;
      //     this.total = total;
      //   }
      // } catch (err) {
      //   console.log(err);
      // } finally {
      //   this.loading = false;
      // }
    },

    /** 搜索按钮操作 */
    handleQuery() {
      this.queryParams.pageNum = 1;
      this.getList();
    },

    /** 重置按钮操作 */
    resetQuery() {
      this.resetForm("queryForm");
      this.queryParams.pageNum = 1;
      this.handleQuery();
    },

    /** 新增按钮操作 */
    handleAdd() {
      this.drawer = true;
      this.mode = "ADD";
    },

    /** 导出按钮操作 */
    // handleExport() {
    //   this.download(
    //     "category/export",
    //     {
    //       ...this.queryParams,
    //     },
    //     `category_${new Date().getTime()}.xlsx`
    //   );
    // },

    // 查看产品详情
    handleLook(row) {
      this.drawer = true;
      this.mode = "LOOK";
      this.cardInfo = row;
    },

    // 关闭弹框
    handleClose() {
      this.drawer = false;
    },

    drawerClose(val) {
      this.handleClose();
      if (val) {
        this.getList();
      }
    },

    handleEdit(row) {
      this.drawer = true;
      this.mode = "EDIT";
      this.cardInfo = row;
    },

    async handleDelete(row) {
      try {
        const res = await this.$confirm(
          '是否确认删除名称为"' + row.cardName + '"的数据项？',
          "提示",
          {
            confirmButtonText: "确定",
            cancelButtonText: "取消",
            type: "warning",
          }
        );

        if (res === "confirm") {
          const { code } = await delCategory(row.cardId);
          if (code === 200) {
            this.resetQuery();
            this.$modal.msgSuccess("删除成功");
          }
        }
      } catch (err) {
        console.log(err);
      }
    },
  },
};
</script>
<style lang="scss" scoped>
.drawer-style {
  &__header {
    line-height: 60px;
    padding-left: 15px;
    font-size: 14px;
    font-weight: 600;
    color: #606266;
  }
  &__content {
    height: calc(100vh - 60px);
    width: 100%;
    border-top: 1px solid rgba(0, 21, 41, 0.15);
  }
}

::v-deep .el-drawer__header {
  margin-bottom: 0;
  padding-bottom: 0;
  margin: 0;
  padding: 0;
}
::v-deep .el-drawer__body {
  padding: 0;
}
</style>
