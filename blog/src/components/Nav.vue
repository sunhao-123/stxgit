<!--
  Copyright (c) 2020 classmate-sun
  [Software Name] is licensed under Mulan PSL v2.
  You can use this software according to the terms and conditions of the Mulan PSL v2.
  You may obtain a copy of Mulan PSL v2 at:
          http://license.coscl.org.cn/MulanPSL2
  THIS SOFTWARE IS PROVIDED ON AN "AS IS" BASIS, WITHOUT WARRANTIES OF ANY KIND,
  EITHER EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO NON-INFRINGEMENT,
  MERCHANTABILITY OR FIT FOR A PARTICULAR PURPOSE.
  See the Mulan PSL v2 for more details.
-->

<template>
  <div>
    <div id="nav">
      <div class="nav-main">
        <div class="n-m-logo" @click="goHome">
          <img src="../assets/img/My-Logo.png" alt="My LOGO" />孙同学的博客
        </div>
        <div class="n-m-login">
          <el-popover
            v-if="ifLogin"
            placement="top-start"
            width="100"
            trigger="hover"
            @click="handleUser(login.user)"
          >
            <p>欢迎登陆：{{login.user}}</p>
            <el-button type="danger" @click="handleLogout" plain size="mini">退出登录</el-button>
            <br />
            <el-button type="primary" @click="ifShowAvatar = true" plain size="mini">修改头像</el-button>
            <a
              :href="'/#/user/' + login.user"
              slot="reference"
              class="img"
              :style="{
                    backgroundImage : 'url('+login.photo+')',
                    position: 'absolute',
                    top: '10px',
                    left: '41px',
                    width: '40px',
                    height: '40px',
                    backgroundPosition: 'center center',
                    backgroundSize: 'cover',
                    borderRadius: '50%',
                    cursor:'pointer'
                  }"
            ></a>
          </el-popover>
          <div class="login" v-else>
            <el-button @click="ifShowLogin = true" type="primary">登录</el-button>
            <el-button @click="ifShowRegister = true" type="success">注册</el-button>
          </div>
        </div>
        <div class="n-m-nav">
          <ul :class="'list'+whichActive">
            <li>
              <router-link to="/">首页</router-link>
            </li>
            <li>
              <router-link to="/blog/0">博客</router-link>
            </li>
            <li>
              <router-link to="/message">留言</router-link>
            </li>
            <li>
              <router-link to="/video">视频</router-link>
            </li>
            <li>
              <router-link to="/diary">更新</router-link>
            </li>
            <li>
              <router-link to="/links">友链</router-link>
            </li>
            <li>
              <router-link to="/about">关于</router-link>
            </li>
          </ul>
        </div>
      </div>
    </div>
    <Login
      :dialogVisible="ifShowLogin"
      @handleClose="closeLogin"
      @handleOpen="gotoRegister"
      @handleForget="gotoForget"
      @ifLogin="postIfLoginFn"
    ></Login>
    <Register :dialogVisible="ifShowRegister" @handleClose="closeRegister" @handleOpen="openLogin"></Register>
    <Forget :dialogVisible="ifShowForget" @handleClose="closeForget" @handleLogin="openLogin"></Forget>
    <Avatar :dialogVisible="ifShowAvatar" @handleClose="closeAvatar"></Avatar>
  </div>
</template>

<script>
import Register from "./Register";
import Login from "./Login";
import Forget from "./Forget";
import Avatar from "./Avatar";
import request from "../api";

const postIfLogin = request.postIfLogin;
const postLogout = request.postLogout;

export default {
  name: "Nav",
  components: {
    Register,
    Login,
    Forget,
    Avatar
  },
  data() {
    return {
      /*路由列表*/
      routerList: [
        "Home",
        "Blog",
        "Message",
        "Video",
        "Diary",
        "Links",
        "About"
      ],

      /*弹窗key值*/
      // alertKey : 0,

      /*是否登录*/
      ifLogin: false,
      /*用户信息*/
      login: {
        user: "",
        photo: ""
      },

      /*登录弹窗和注册弹窗*/
      ifShowLogin: false,
      ifShowRegister: false,
      ifShowForget: false,
      ifShowAvatar: false
    };
  },
  computed: {
    whichActive() {
      let index = this.routerList.indexOf(this.$route.name);
      if (this.$route.name === "Article") index = 1;

      return index + 1;
    }
  },
  methods: {
    closeRegister(bool) {
      this.ifShowRegister = false;
      this.ifShowLogin = bool;
    },
    closeForget(bool) {
      this.ifShowForget = false;
      this.ifShowLogin = bool;
    },
    closeLogin() {
      this.ifShowLogin = false;
    },
    openLogin(bool) {
      this.ifShowLogin = true;
      this.ifShowRegister = bool;
      this.ifShowForget = bool;
    },
    gotoRegister(bool) {
      this.ifShowRegister = true;
      this.ifShowLogin = bool;
      this.ifShowForget = bool;
    },
    gotoForget(bool) {
      this.ifShowForget = true;
      this.ifShowRegister = bool;
      this.ifShowLogin = bool;
    },
    closeAvatar() {
      this.ifShowAvatar = false;
    },
    postIfLoginFn() {
      postIfLogin().then(res => {
        if (res.data.userInfo) {
          this.ifLogin = true;
          this.login.user = res.data.userInfo.user;
          this.login.photo = res.data.userInfo.photo;
        } else {
          this.ifLogin = false;
        }
      });
    },
    goHome() {
      this.$router.push(`/`);
    },
    handleLogout() {
      postLogout()
        .then(() => {
          this.$message({
            message: "退出登录成功",
            type: "success",
            duration: 2000
          });
          this.postIfLoginFn();
        })
        .catch(() => {
          this.$message({
            message: "退出失败…",
            type: "error",
            duration: 2000
          });
        });
    },
    handleUser(name) {
      console.log(name);
    }
  },

  mounted() {
    postIfLogin().then(res => {
      if (res.data.userInfo) {
        this.ifLogin = true;
        this.login.user = res.data.userInfo.user;
        this.login.photo = res.data.userInfo.photo;
      } else {
        this.ifLogin = false;
      }
    });
  }
};
</script>

<style lang="less" scoped>
#nav {
  overflow: hidden;
  position: fixed;
  z-index: 999;
  top: 0;
  left: 0;
  width: 100%;
  min-width: 1260px;
  height: 60px;
  background-color: rgba(245, 250, 252, 0.9);
  border-bottom: 1px solid #e8e9e7;
  user-select: none;
  > .nav-main {
    box-sizing: border-box;
    width: 100%;
    max-width: 1360px;
    height: 60px;
    margin: 0 auto;
    padding: 0 50px;
    .n-m-logo {
      float: left;
      width: 300px;
      height: 60px;
      line-height: 60px;
      text-align: left;
      color: #444;
      font-size: 40px;
      font-family: FZJianQRTJW;
      img {
        height: 45px;
        width: auto;
        vertical-align: middle;
        margin-right: 10px;
      }
    }
    .n-m-login {
      position: relative;
      float: right;
      width: 122px;
      height: 60px;
      line-height: 60px;
      .el-button {
        padding: 9px 13px;
      }
      > div.userInfo {
        width: 100%;
        height: 100%;
      }
    }
    .n-m-nav {
      float: right;
      width: 576px;
      height: 60px;
      margin-right: 8%;
      ul {
        display: flex;
        width: 100%;
        height: 100%;
        li {
          width: 85px;
          flex: 1;
          a {
            position: relative;
            display: block;
            width: 85px;
            height: 100%;
            text-align: center;
            line-height: 60px;
            color: #212220;
            font-weight: 400;
            font-size: 15px;
            transition: color 0.5s;
            &::after {
              position: absolute;
              bottom: 0;
              left: 0;
              right: 0;
              margin: 0 auto;
              content: "";
              width: 0;
              height: 2px;
              background-color: #6bc30d;
              transition: width 0.5s;
            }
            &:hover {
              color: #6bc30d;
              &::after {
                width: 90%;
              }
            }
          }
        }
        &.list1 li:nth-child(1),
        &.list2 li:nth-child(2),
        &.list3 li:nth-child(3),
        &.list4 li:nth-child(4),
        &.list5 li:nth-child(5),
        &.list6 li:nth-child(6),
        &.list7 li:nth-child(7) {
          a {
            color: #6bc30d;
            &::after {
              width: 90%;
            }
          }
        }
      }
    }
  }
}
@media (max-width: 992px) {
  #nav {
    width: 100%;
    min-width: 100px;
    > .nav-main {
      padding: 0 20px;
      .n-m-nav {
        width: 506px;
        margin-right: 10px;
        ul{
          li{
            width: 72.281px;
          }
        }
      }
    }
  }
}
</style>



















