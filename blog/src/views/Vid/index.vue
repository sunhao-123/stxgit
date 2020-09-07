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
  <div class="NotFound">
    <Nav></Nav>
    <div class="search">
      <div class="searchBox">
        <el-input
          placeholder="搜索视频"
          v-model="name"
          class="input-with-select"
          @keyup.enter.native="handleSearch"
        >
          <el-button slot="append" icon="el-icon-search" @click="handleSearch"></el-button>
        </el-input>
      </div>
    </div>
    <div class="searchList" v-if="search">
      <el-table :data="searchList" max-height="300">
        <el-table-column prop="name" :label="searchTitle">
          <template slot-scope="scope">
            <div @click="handleClick(scope.row.id)">{{ scope.row.name }}</div>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <div class="videobox" :style="style">
      <div class="video" v-for="item in videoList" :key="item" @click="handleClick(item.id)">
        <img :src="item.img" alt />
        <p>{{item.name}}</p>
      </div>
    </div>
    <p class="bottomTip">
      声明：本站全部视频资源均来自<a href="http://www.zuidazy5.net/" target="_blank">最大资源网</a>，如有任何版权问题与本站无关。
    </p>
  </div>
</template>

<script>
import Nav from "../../components/Nav";
import request from "../../api";

const postSearchVideo = request.postSearchVideo;
const postIfLogin = request.postIfLogin;
const postGetList = request.postGetList;
export default {
  name: "NotFound",
  components: { Nav },
  data() {
    return {
      fullHeight: document.documentElement.clientHeight,
      style: {},
      name: "",
      videoList: [],
      searchList: [],
      searchTitle: "",
      search: false
    };
  },
  watch: {
    fullHeight(val) {
      if (!this.timer) {
        this.fullHeight = val;
        this.timer = true;
        let that = this;
        setTimeout(function() {
          that.timer = false;
        }, 400);
      }
      this.style = {
        height: this.fullHeight - 173 + "px"
      };
    }
  },
  methods: {
    goHome() {
      this.$router.push({ path: `/` });
    },
    handleSearch() {
      this.search = true;
      this.searchTitle = `正在搜索······`;
      this.searchList = [];
      postSearchVideo(this.name).then(res => {
        this.search = true;
        this.searchList = res.data;
        this.searchTitle = `搜索结果  共${res.data.length}条`;
      });
    },
    handleClick(id) {
      this.$router.push({ path: `/watch/${id}` });
      this.search = false;
      this.searchList = [];
    }
  },
  mounted() {
    const that = this;
    window.onresize = () => {
      return (() => {
        window.fullHeight = document.documentElement.clientHeight;
        that.fullHeight = window.fullHeight;
      })();
    };
    this.style = {
      height: this.fullHeight - 173 + "px"
    };
    postIfLogin().then(res => {
      if (!res.data.userInfo) {
        this.$alert("您还未登录哦，无法访问此页面！！", "❌警告", {
          confirmButtonText: "确定",
          callback: action => {
            this.$router.push("/");
          }
        });
      }
    });
    postGetList().then(res => {
      this.videoList = res.data.data;
    });
  }
};
</script>

<style scoped lang="less">
.NotFound {
  background-color: #fff;
}

.search {
  width: 1200px;
  margin: 80px auto 0;
  .searchBox {
    margin-left: 50px;
    width: 400px;
  }
}
.searchList {
  margin: 10px auto;
  width: 1200px;
}
.videobox {
  width: 1200px;
  background-color: #fff;
  margin: 20px auto 0;
  .video {
    width: 220px;
    height: 325px;
    margin: 10px;
    float: left;
    img {
      width: 220px;
      height: 307px;
      transition: 0.3s;
      &:hover {
        transform: translateY(-10px);
        box-shadow: 0 10px 5px #888;
      }
    }
    p {
      width: 220px;
      height: 30px;
      font-size: 18px;
      line-height: 30px;
      text-align: center;
    }
  }
}
.bottomTip {
  display: block;
  width: 100%;
  height: 13px;
  font-size: 12px;
  line-height: 13px;
  text-align: center;
}
@media (max-width: 992px) {
  .search {
    width: 100%;
  }
  .searchList {
    width: 100%;
  }
  .videobox {
    width: 100%;
  }
}
</style>