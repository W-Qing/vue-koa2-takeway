<template>
  <div class="search-panel">
    <div class="search-panel">
      <el-row class="m-header-searchbar">
        <el-col 
          :span="3"
          class="left">
          <img 
            src="~assets/img/weixin.png"
            alt="味心">
        </el-col>
        <el-col 
          :span="15"
          class="center">
          <div class="wrapper">
            <el-input 
              v-model="search"
              @focus="focus"
              @blur="blur"
              @input="input"
              placeholder="搜索商家或地点"/>
            <button class="el-button el-button--primary">
              <i class="el-icon-search"/>
            </button>
            <!-- 由isHotPlace和is 来渲染不同的Dom -->
            <dl 
              class="hotPlace" 
              v-if="isHotPlace">
              <dt>热门搜索</dt>
              <dd
                v-for="(item,idx) in $store.state.home.hotPlace.slice(0,4)"
                :key="idx">
                <a :href="'/products?keyword='+encodeURIComponent(item.name)">{{ item.name }}</a>
              </dd>
            </dl>
            <dl
              v-if="isSearchList"
              class="searchList">
              <dd
                v-for="(item,idx) in searchList"
                :key="idx">
                <a :href="'/products?keyword='+encodeURIComponent(item.name)">{{ item.name }}</a>
              </dd>
            </dl>
          </div>
          <p class="suggest">
            <a
              v-for="(item,idx) in $store.state.home.hotPlace.slice(0,4)"
              :key="idx"
              :href="'/products?keyword='+encodeURIComponent(item.name)">{{ item.name }}</a>
          </p>
          <ul class="nav">
            <li><nuxt-link
              to="/"
              class="takeout">味心生活</nuxt-link></li>
            <li><a href="https://maoyan.com/">猫眼电影</a></li>
            <li><nuxt-link
              to="/"
              class="hotel">味心酒店</nuxt-link></li>
            <li><nuxt-link
              to="/"
              class="apartment">民宿/公寓</nuxt-link></li>
          </ul>
        </el-col>
        <el-col 
          :span="6"
          class="right">
          <ul class="security">
            <li>
              <i class="refund"/>
              <p class="txt">随时退</p>
            </li>
            <li>
              <i class="single"/>
              <p class="txt">不满意免单</p>
            </li>
            <li>
              <i class="overdue"/>
              <p class="txt">过期退</p>
            </li>
          </ul>
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
import _ from 'lodash'
export default {
  data(){
    return {
      search:'',
      isFocus:false,
      hotPlace:[],
      searchList:[]
    }
  },
  // 根据判断搜索框是否聚焦isFocus和是否有输入值search来决定模板显示
  computed: {
    isHotPlace: function() {
      return this.isFocus && !this.search
    },
    isSearchList: function() {
      return this.isFocus && this.search
    }
  },
  methods: {
    focus: function() {
      this.isFocus = true
    },
    blur: function() {
      let self = this
      setTimeout(function(){
        self.isFocus = false
      },200)
    },
    // 监听input输入的内容，用来更新推荐列表
    input:_.debounce(async function(){
      let self=this;
      let city=self.$store.state.geo.position.city.replace('市','')
      self.searchList=[]
      let {status,data:{top}}=await self.$axios.get('/search/top',{
        params:{
          input:self.search,
          city
        }
      })
      self.searchList=top.slice(0,10)
    },300)
  }
}
</script>
<style lang="css">
</style>
