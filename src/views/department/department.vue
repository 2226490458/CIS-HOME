<template>
  <div>
    <div>
      <el-form
        ref="form"
        :inline="true"
        :model="selectForm"
        label-width="100px"
      >
        <el-form-item label="输入部门名称">
          <el-input v-model="selectForm.deptName"></el-input>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" size="small" @click="select(1)"
            >查询</el-button
          >
        </el-form-item>

        <el-form-item>
          <el-button type="primary" size="small" @click="deleteListDept"
            >删除</el-button
          >
        </el-form-item>
      </el-form>
    </div>

    <el-table
      :data="talbeData"
      border
      @selection-change="handleSelectionChange"
      style="margin-bottom: 20px"
    >
      <el-table-column type="selection"> </el-table-column>
      <el-table-column label="部门名称" prop="deptName"> </el-table-column>
      <el-table-column label="详细信息" prop="deptRemark"> </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button type="primary" size="small" @click="handleEdit(scope.row)"
            >编辑</el-button
          >
          <el-button type="danger" size="small" @click="deleteDept(scope.row)"
            >删除</el-button
          >
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      background
      layout="prev, pager, next"
      :total="dataTotal"
      @current-change="changePage"
    >
    </el-pagination>
    <el-dialog title="提示" :visible.sync="dialogVisible" width="30%">
      <span>
        <el-form ref="form" :model="editForm" label-width="80px">
          <el-form-item label="部门名称">
            <el-input v-model="editForm.deptName"></el-input>
          </el-form-item>

          <el-form-item label="详细描述">
            <el-input v-model="editForm.deptRemark"></el-input>
          </el-form-item>
        </el-form>
      </span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="edit">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectForm: {
        deptName: ""
      },

      dialogVisible: false,

      editForm: {
        deptName: "",
        deptRemark: ""
      },
      updateItemId: null,
      talbeData: [],
      selectedTable: [],
      dataTotal: 20
    };
  },
  methods: {
    select(page = 1) {
      this.$http({
        url: "/getDept",
        method: "GET",
        params: {
          deptName: this.selectForm.deptName,
          page: page,
          size: this.$STATICE_SETTING.pageSize
        }
      }).then(res => {
        const data = res.data;
        if ((data.status >= 200 && data.status < 300) || data.status === 304) {
          this.talbeData = data.data.list;
          this.dataTotal = data.data.total;
        } else {
          this.$message({ type: "warning", message: data.message });
        }
      });
    },

    handleEdit(item) {
      this.dialogVisible = true;
      this.updateItemId = item.deptId;
    },

    edit() {
      if (
        this.updateItemId === null ||
        this.editForm.deptName === "" ||
        this.editForm.deptRemark === ""
      ) {
        this.$message({ type: "warning", message: "修改字段不能为空!" });
        return;
      }
      this.$http({
        url: "/updateDept",
        method: "POST",
        data: {
          deptId: this.updateItemId,
          deptName: this.editForm.deptName,
          deptRemark: this.editForm.deptRemark
        }
      }).then(res => {
        const data = res.data;
        this.dialogVisible = false;
        this.$message({ type: "warning", message: data.message });
        this.select();
      });
    },

    deleteSingleDept(detailInfo) {
      this.$confirm(
        `此操作将永久删除${detailInfo.deptName}信息, 是否继续？`,
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning"
        }
      )
        .then(() => {
          this.$http({
            url: "/deleteDept",
            method: "POST",
            data: {
              ids: [detailInfo.deptId]
            }
          }).then(res => {
            const data = res.data;
            this.$message({
              type: "success",
              message: "删除成功!"
            });
            this.select();
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消!"
          });
        });
    },

    deleteListDept() {
      this.$confirm("此操作将永久删除所选部门信息, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          let ids = [];
          this.selectedTable.forEach(val => {
            ids.push(val.deptId);
          });
          this.$http({
            url: "/deleteDept",
            method: "POST",
            data: {
              ids
            }
          }).then(res => {
            const data = res.data;
            if (
              (data.status >= 200 && data.status < 300) ||
              data.status === 304
            ) {
              this.$message({
                type: "success",
                message: "删除成功!"
              });
            } else {
              this.$message({ type: "warning", message: data.message });
            }
            this.select();
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消!"
          });
        });
    },

    handleSelectionChange(selected) {
      this.selectedTable = selected;
    },

    changePage(page) {
      this.select(page);
    }
  },
  mounted() {
    this.select();
  }
};
</script>

<style>
</style>