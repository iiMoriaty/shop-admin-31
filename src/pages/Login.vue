<template>
  <div class="form-wrapper">
    <el-form ref="form" :model="form" :rules="rules" label-width="80px" class="form">
      <el-form-item label="用户" prop="username">
        <el-input v-model="form.username"></el-input>
      </el-form-item>

      <el-form-item label="密码" prop="password">
        <el-input v-model="form.password"></el-input>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="onSubmit">登录</el-button>
        <el-button>取消</el-button>
      </el-form-item>
    </el-form>
  </div>
</template> 

<script>
export default {
  data() {
    return {
      form: {
        username: "",
        password: ""
      },

      rules: {
        username: [
          //required:表示必须输入，message:提示，trigger：失去焦点
          { required: true, message: "请输入用户名", trigger: "blur" }
        ],

        password: [
          //required:表示必须输入，message:提示，trigger：失去焦点
          { required: true, message: "请输入密码", trigger: "blur" }
        ]
      }
    };
  },
  methods: {
    onSubmit() {
      //提交到后台的数据
      const data = {
        uname: this.form.username,
        upwd: this.form.password
      };

      //如果表单的验证不通过，那么就不提交表单
      this.$refs.form.validate(valid => {
        if (valid) {
          //调用axios
          this.$axios({
            //请求地址
            url: "http://localhost:8899/admin/account/login",
            method: "POST",
            data,
            //处理跨域请求
            withCredentials:true
          }).then(res => {
            //解构并赋值
            const { message, status } = res.data;

            if (status === 0) {
              this.$router.push("/");
            }

            if (status === 1) {
              this.$message.error(message);
            }
          });
        }
      });
    }
  }
};
</script>


<style scoped>
.form-wrapper {
  width: 100%;
  position: absolute;
  top: 0;
  bottom: 0;
}

.form {
  width: 300px;
  position: absolute;
  top: 50%;
  margin-top: -95px;
  left: 50%;
  margin-left: -200px;
}
</style>
