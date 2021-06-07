<template>
  <div class="login-container">
    <el-form ref="form" :model="form" class="login-form" auto-complete="on" label-position="left">
      <div class="title-container">
        <h3 class="title">注册账户</h3>
      </div>

      <el-form-item>
        <span class="svg-container">
          <svg-icon icon-class="user" />
        </span>
        <el-input
          v-model="form.userName"
          placeholder="用户名"
          name="userName"
          type="text"
          tabindex="1"
          auto-complete="on"
        />
      </el-form-item>

      <el-form-item>
        <span class="svg-container">
          <svg-icon icon-class="users" />
        </span>
        <el-input
          ref="realName"
          v-model="form.realName"
          placeholder="真实姓名"
          name="userName"
          type="text"
          tabindex="3"
          auto-complete="on"
        />
      </el-form-item>

      <el-tooltip v-model="capsTooltip" content="Caps lock is On" placement="right" manual>
        <el-form-item prop="password">
          <span class="svg-container">
            <svg-icon icon-class="password" />
          </span>
          <el-input
            :key="passwordType"
            ref="password"
            v-model="form.password"
            :type="passwordType"
            placeholder="密码"
            name="password"
            tabindex="2"
            auto-complete="on"
            @keyup.native="checkCapslock"
            @blur="capsTooltip = false"
            @keyup.enter.native="handleRegister"
          />
          <span class="show-pwd" @click="showPwd">
            <svg-icon :icon-class="passwordType === 'password' ? 'eye' : 'eye-open'" />
          </span>
        </el-form-item>
      </el-tooltip>

      <!-- <el-checkbox v-model="loginForm.remember" style="margin-bottom: 20px;margin-left: 5px;">记住密码</el-checkbox> -->

      <el-button
        :loading="loading"
        type="primary"
        style="width:100%;margin-bottom:20px;"
        @click="submitForm"
      >注册</el-button>
      <div style="width:100%;margin-bottom:20px;position:relative">
        <span
          @click="goLogin"
          style="color:#4399fc;cursor:pointer;font-size: 14px;position:absolute;right:0px"
        >返回登录</span>
      </div>
    </el-form>

    <div class="account-foot-copyright">
      <span>Copyright © 2021 HHStudy Group 版权所有</span>
    </div>
  </div>
</template>

<script>
import { mapMutations } from "vuex";
import { mapGetters, mapActions } from "vuex";
import loginApi from "@/api/login";
import userApi from "@/api/user";

export default {
  name: "Register",
  data() {
    const validateUsername = (rule, value, callback) => {
      //   if (value.length < 5) {
      //     callback(new Error("用户名不能少于5个字符"));
      //   } else {
      callback();
      //   }
    };
    const validatePassword = (rule, value, callback) => {
      //   if (value.length < 5) {
      //     callback(new Error("密码不能少于5个字符"));
      //   } else {
      callback();
      //   }
    };
    return {
      form: {
        id: null,
        userName: "",
        password: "",
        realName: "",
        role: 3,
        status: 1,
        age: "",
        sex: "",
        birthDay: null,
        phone: null
      },
      formLoading: false,
      passwordType: "password",
      capsTooltip: false,
      loading: false,
      showDialog: false,
      rules: {
        userName: [
          { required: true, message: "请输入用户名", trigger: "blur" }
        ],
        realName: [
          { required: true, message: "请输入真实姓名", trigger: "blur" }
        ]
      },
      loginForm: {
        userName: "admin",
        password: 123456,
        remember: false
      }
    };
  },
  created() {
    let _this = this;
    loginApi
      .login(this.loginForm)
      .then(function(result) {
        if (result && result.code === 1) {
          _this.setUserName(_this.loginForm.userName);
          //   _this.$router.push({ path: "/" });
        } else {
          _this.loading = false;
          _this.$message({
            message: result.message,
            type: "error"
          });
        }
      })
      .catch(function(reason) {
        _this.loading = false;
      });
    // window.addEventListener('storage', this.afterQRScan)
  },
  mounted() {},
  destroyed() {
    // window.removeEventListener('storage', this.afterQRScan)
  },
  methods: {
    goLogin() {
      console.log(this.$router);
      this.$router.replace("/login");
    },
    checkCapslock({ shiftKey, key } = {}) {
      if (key && key.length === 1) {
        // eslint-disable-next-line no-mixed-operators
        if (
          (shiftKey && key >= "a" && key <= "z") ||
          (!shiftKey && key >= "A" && key <= "Z")
        ) {
          this.capsTooltip = true;
        } else {
          this.capsTooltip = false;
        }
      }
      if (key === "CapsLock" && this.capsTooltip === true) {
        this.capsTooltip = false;
      }
    },
    showPwd() {
      if (this.passwordType === "password") {
        this.passwordType = "";
      } else {
        this.passwordType = "password";
      }
      this.$nextTick(() => {
        this.$refs.password.focus();
      });
    },
    submitForm() {
      let _this = this;
      this.$refs.form.validate(valid => {
        if (valid) {
          console.log(userApi);
          console.log(this.form);
          this.formLoading = true;
          userApi
            .createUser(this.form)
            .then(data => {
              console.log(data);
              if (data.code === 1) {
                _this.$message.success(data.message);
                _this.delCurrentView(_this).then(() => {
                  _this.$router.push("/login");
                  console.log(data);
                });
              } else {
                _this.$message.error(data.message);
                _this.formLoading = false;
              }
            })
            .catch(e => {
              _this.formLoading = false;
            });
        } else {
          //   return false;
        }
      });
    },
    ...mapActions("tagsView", { delCurrentView: "delCurrentView" })
    // ...mapMutations("user", ["setUserName"])
  },
  computed: {
    ...mapGetters("enumItem", ["enumFormat"])
  }
};
</script>

<style lang="scss">
/* Detail see https://github.com/PanJiaChen/vue-element-admin/pull/927 */

$bg: #283443;
$light_gray: #fff;
$cursor: #fff;

@supports (-webkit-mask: none) and (not (cater-color: $cursor)) {
  .login-container .el-input input {
    color: $cursor;
  }
}

/* reset element-ui css */
.login-container {
  .el-input {
    display: inline-block;
    height: 47px;
    width: 85%;

    input {
      background: transparent;
      border: 0px;
      -webkit-appearance: none;
      border-radius: 0px;
      padding: 12px 5px 12px 15px;
      color: $light_gray;
      height: 47px;
      caret-color: $cursor;

      &:-webkit-autofill {
        box-shadow: 0 0 0px 1000px $bg inset !important;
        -webkit-text-fill-color: $cursor !important;
      }
    }
  }

  .el-form-item {
    border: 1px solid rgba(255, 255, 255, 0.1);
    background: rgba(0, 0, 0, 0.1);
    border-radius: 5px;
    color: #454545;
  }
}
</style>

<style lang="scss" scoped>
$bg: #2d3a4b;
$dark_gray: #889aa4;
$light_gray: #eee;

.login-container {
  min-height: 100%;
  width: 100%;
  background-color: $bg;
  overflow: hidden;

  .login-form {
    position: relative;
    width: 520px;
    max-width: 100%;
    padding: 30px 50px 10px 50px;
    margin: 120px auto auto auto;
    overflow: hidden;
    background: rgba(252, 254, 255, 0.11);
  }

  .tips {
    font-size: 14px;
    color: #fff;
    margin-bottom: 10px;

    span {
      &:first-of-type {
        margin-right: 16px;
      }
    }
  }

  .svg-container {
    padding: 6px 5px 6px 15px;
    color: $dark_gray;
    vertical-align: middle;
    width: 30px;
    display: inline-block;
  }

  .title-container {
    position: relative;

    .title {
      font-size: 26px;
      color: $light_gray;
      margin: 0px auto 40px auto;
      text-align: center;
      font-weight: bold;
    }
  }

  .show-pwd {
    position: absolute;
    right: 10px;
    top: 7px;
    font-size: 16px;
    color: $dark_gray;
    cursor: pointer;
    user-select: none;
  }

  .thirdparty-button {
    position: absolute;
    right: 0;
    bottom: 6px;
  }

  @media only screen and (max-width: 470px) {
    .thirdparty-button {
      display: none;
    }
  }
}
</style>
