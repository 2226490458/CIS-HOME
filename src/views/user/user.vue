<template>
  <div>
    <el-form :inline="true" :model="formInline" class="demo-form-inline">
      <el-form-item label="用户名">
        <el-input v-model="formInline.user" placeholder="用户名"></el-input>
      </el-form-item>
      <el-form-item label="状态">
        <el-select v-model="formInline.region" placeholder="状态">
          <el-option label="1" value="1"></el-option>
          <el-option label="2" value="2"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">查询</el-button>
        <el-button type="danger" @click="de">删除</el-button>
      </el-form-item>
    </el-form>

    <el-table class="table" :data="talbeData" border>
      <el-table-column type="selection"> </el-table-column>
      <el-table-column label="登录名" prop="loginName"> </el-table-column>
      <el-table-column label="密码" prop="userPwd"> </el-table-column>
      <el-table-column label="用户名" prop="userName"> </el-table-column>
      <el-table-column label="状态" prop="status"> </el-table-column>
      <el-table-column label="创建时间" prop="userCreate"> </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button type="danger" size="small" @click="edit(scope.row)">编辑</el-button>
          <el-button type="danger" size="small" @click="deleteUser(scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination background layout="prev, pager, next" :total="50">
    </el-pagination>

    <el-dialog :title="dialogTitle" :visible.sync="dialogFormVisible">

      <el-form :model="form">
        <el-form-item label="姓名">
          <el-input v-model="form.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="登录名">
          <el-input v-model="form.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="状态">
          <el-select v-model="form.region" placeholder="状态">
            <el-option label="1" value="1"></el-option>
            <el-option label="2" value="2"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogFormVisible = false"
          >确 定</el-button
        >
      </div>
      
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      dialogFormVisible: false,
      dialogTitle: "",
      form: {
        name: "",
        region: ""
      },
      formInline: {
        user: "",
        region: ""
      },
      talbeData: [
        {
          loginName: "测试员1",
          userPwd: "123",
          userName: "重庆理工大学",
          status: 0,
          userCreate: "2021年6月17日"
        },
        {
          loginName: "测试员2",
          userPwd: "123",
          userName: "重庆理工大学",
          status: 0,
          userCreate: "2021年6月17日"
        },
        {
          loginName: "测试员3",
          userPwd: "123",
          userName: "重庆理工大学",
          status: 0,
          userCreate: "2021年6月17日"
        }
      ]
    };
  },


  methods: {

    


    edit() {
     if(this.form.name === "" || this.form.region===""){
       this.$message({type: 'warning', message: "修改字段不能为空！"});
       return
     }
     this.$http({
       url: "http://localhost:8080/updateUser",
       method: "POST",
       data: {
         loginName:this.form.name,
         status:this.form.region,
       }
     }).then(res =>{
       const data = res.data;
       this.dialogFormVisible = false;
       this.$message({type: 'warning', message:data.message});
       this.onSubmit();
     })
    },
    onSubmit() {  //查询
      this.$http({
        url: "http://localhost:8080/getUser",
        method: "GET",
        params: {
          userName: this.formInline.user,
          status: this.formInline.region,
        },
      }).then(res =>{
        const data = res.data;
        if((data.status >= 200 && data.status<300) || data.status === 304){
          this.talbeData = data.data;
        }else{
          this.$message({ type: 'warning',message: data.message})
        }
      })
    },

    /*
    deleteUser(){
      this.$confirm('此操作将永久删除该部门信息，是否继续','提示',{
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
                      url: "http://localhost:8080/deleteUser",
                      method: 'POST',
                      data: {
                        ids:[]
                      }
        }).then(res => {
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
          this.onSubmit();
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消！'
        });
      });
    }
*/
  }
};
</script>

<style lang="css" scoped>
.table {
  margin-bottom: 20px;
}
</style>