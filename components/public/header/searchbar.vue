<template>
  <div class="search-panel">
    <el-row class="m-header-searchbar">
      <el-col :span="3" class="left">
        <img
          src="//s0.meituan.net/bs/fe-web-meituan/fa5f0f0/img/logo.png"
          alt="美团"
        />
      </el-col>
      <el-col :span="15" class="center">
        <div class="wrapper">
          <el-input
            placeholder="搜索商家或地点"
            @focus="focus"
            @blur="blur"
            @input="input"
            v-model="search"
          />
          <button class="el-button el-button--primary">
            <i class="el-icon-search" />
          </button>
          <dl class="hotPlace" v-if="isHotPlace">
            <dt>热门搜索</dt>
            <dd
              v-for="(item, index) in $store.state.home.hotPlace.slice(0, 4)"
              :key="index"
            >
              {{ item.name }}
            </dd>
          </dl>
          <dl class="searchList" v-if="isSearchList">
            <dd v-for="(item, index) in searchList" :key="index">
              {{ item.name }}
            </dd>
          </dl>
        </div>
        <p class="suggest">
          <a
            href="#"
            v-for="(item, index) in $store.state.home.hotPlace.slice(0, 5)"
            :key="index"
            >{{ item.name }}</a
          >
        </p>
        <ul class="nav">
          <li>
            <nuxt-link to="/" class="takeout">美团外卖</nuxt-link>
          </li>
          <li>
            <nuxt-link to="/" class="movie">猫眼电影</nuxt-link>
          </li>
          <li>
            <nuxt-link to="/" class="hotel">美团酒店</nuxt-link>
          </li>
          <li>
            <nuxt-link to="/" class="apartment">民宿/公寓</nuxt-link>
          </li>
          <li>
            <nuxt-link to="/" class="business">商家入驻</nuxt-link>
          </li>
        </ul>
      </el-col>
      <el-col :span="6" class="right">
        <ul class="security">
          <li>
            <i class="refund" />
            <p class="txt">随时退</p>
          </li>
          <li>
            <i class="single" />
            <p class="txt">不满意免单</p>
          </li>
          <li>
            <i class="overdue" />
            <p class="txt">过期退</p>
          </li>
        </ul>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import _ from "lodash";
import { async } from "q";

export default {
  data() {
    return {
      search: "",
      isFocus: false,
      hotPlace: [],
      searchList: []
    };
  },
  computed: {
    isHotPlace: function() {
      return this.isFocus && !this.search;
    },
    isSearchList: function() {
      return this.isFocus && this.search;
    }
  },
  methods: {
    focus: function() {
      this.isFocus = true;
    },
    blur: function() {
      let that = this;
      setTimeout(() => {
        that.isFocus = false;
      }, 200);
    },
    input: _.debounce(async function() {
      let self = this;
      let city = self.$store.state.geo.position.city.replace("市", "");
      self.searchList = [];
      let {
        status,
        data: { top }
      } = await self.$axios.get("/search/top", {
        params: {
          input: self.search,
          city
        }
      });
      self.searchList = top.slice(0, 10);
    }, 300)
  }
};
</script>

<style lang="scss" >
</style>