<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/basedata' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>设置</el-breadcrumb-item>
      <el-breadcrumb-item>密码设置</el-breadcrumb-item>
    </el-breadcrumb>
    <el-card class="box-card">
      <div
        slot="header"
        class="clearfix"
      >

        <span>修改密码</span>

      </div>

      <el-form
        :model="ruleForm"
        status-icon
        :rules="rules"
        ref="ruleForm"
        label-width="100px"
      >
        <el-form-item
          label="密码"
          prop="pass"
        >
          <el-input
            type="password"
            v-model="ruleForm.pass"
            autocomplete="off"
            style="width: 300px;"
            placeholder=" 6-16 位的字母、数字组合"
          ></el-input>
        </el-form-item>
        <el-form-item
          label="确认密码"
          prop="checkPass"
        >
          <el-input
            type="password"
            v-model="ruleForm.checkPass"
            autocomplete="off"
            style="width: 300px;"
            placeholder=" 6-16 位的字母、数字组合"
          ></el-input>
        </el-form-item>
        <el-form-item>
          <el-tooltip
            class="item"
            effect="dark"
            content="点击提交"
            placement="top-start"
          >
            <el-button
              type="primary"
              @click="submitForm('ruleForm')"
            >提交</el-button>
          </el-tooltip>
          <el-button @click="resetForm('ruleForm')">重置</el-button>
        </el-form-item>
      </el-form>

    </el-card>

  </div>
</template>

<script>
export default {
  data () {

    var validatePass = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入密码'));
      } else {
        if (this.ruleForm.checkPass !== '') {
          this.$refs.ruleForm.validateField('checkPass');
        }
        callback();
      }
    };
    var validatePass2 = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请再次输入密码'));
      } else if (value !== this.ruleForm.pass) {
        callback(new Error('两次输入密码不一致!'));
      } else {
        callback();

      }
    };
    return {
      pic: [
        { url: require('@/assets/login.jpg') },
        { url: require('@/assets/建模.webp') },
        { url: require('@/assets/蓝桥杯.webp') },
        { url: require('@/assets/建模.webp') },

      ],
      ruleForm: {
        pass: '',
        checkPass: '',

      },
      rules: {
        pass: [
          { validator: validatePass, trigger: 'blur' },
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 4, max: 16, message: '长度在 4 到 16 个字符', trigger: 'blur' }
        ],
        checkPass: [
          { validator: validatePass2, trigger: 'blur' },
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 6, max: 16, message: '长度在 6 到 16 个字符', trigger: 'blur' }
        ],

      }
    };

  },
  methods: {
    resetForm (formName) {
      this.$refs[formName].resetFields();
    },
    submitForm (formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          let form = new FormData();
          form.append('passWord', this.ruleForm.pass);
          this.$http({
            url: '/changePassWord',
            method: "put",
            headers: {
              token: window.sessionStorage.getItem('token'),
              "Content-Type": "multipart/form-data",
            },
            data: form
          }).then((res) => {

            if (res.data.code === 1) {
              this.$message.success('修改密码成功');
              this.$refs[formName].resetFields();
            }


            else {
              this.$message.error('修改失败')
            }
            console.log(res);
          })

        } else {
          this.$message.error('提交失败');
          return false;
        }
      });
    },
  }

}
</script>

<style scoped>
.box-card {
  margin-top: 20px;
  height: 600px;
}
</style>