<!--
 * @Descripttion: 首页组件
 * @Author: congz
 * @Date: 2020-06-04 11:22:40
 * @LastEditors: congz
 * @LastEditTime: 2020-08-12 21:20:08
-->

<template>
  <div class="home" id="home" name="home">
    <!-- 轮播图 -->
    <div class="block">
      <el-carousel height="460px">
        <el-carousel-item v-for="item in carousels" :key="item.id">
          <router-link :to="{ path: '/goods/details', query: {productID:item.product_id} }">
            <img style="height:460px;" v-lazy="item.img_path" />
          </router-link>
        </el-carousel-item>
      </el-carousel>
    </div>
    <!-- 轮播图END -->
    <div class="main-box">
      <div class="main">
        <!-- 家电商品展示区域 -->
        <div class="appliance" id="promo-menu">
          <div class="box-hd">
            <div class="title"><h1 style="font-size: 26px;font-weight: 500;line-height: 1.2;">探索优质项目</h1></div>
            <div class="more" id="more">
              <MyMenu :val="2" @fromChild="getChildMsg">
                <span slot="1">热门</span>
                <span slot="2">查看更多</span>
              </MyMenu>
            </div>
          </div>
          <div class="box-bd">
            <div class="promo-list">
              <ul>
                <router-link :to="{ path: '/goods/details', query: {productID:21} }">
                  <li>
                    <img
                        :src="'http://47.115.85.237:3000/public/imgs/appliance/appliance-promo1.png'"
                    />
                  </li>
                </router-link>
                <router-link :to="{ path: '/goods/details', query: {productID:22} }">
                  <li>
                    <img
                        :src="'http://47.115.85.237:3000/public/imgs/appliance/appliance-promo2.png'"
                    />
                  </li>
                </router-link>
              </ul>
            </div>
            <div class="list">
              <MyList :list="applianceList" :isMore="true"></MyList>
            </div>
          </div>
        </div>
        <!-- 家电商品展示区域END -->
      </div>
    </div>

    <!-- 关于我们 -->
    <div class="introduce">
      <section class="container">
        <el-row :gutter="30" class="d-flex  align-items-center">

          <el-col :span="12" class="">
            <div class="text-introduce">
              <p class="fs-30">众筹网 SINGAPOREIT PTE LTD</p><br/>
              <p class="fs-20">我们是一个专注于发现新奇好物平台。为创作者发声。</p><br/>
            <div class="text-introduce01">
              <p>我们在塑造一个发现新奇好物的平台，为人们带来更多乐趣和可能性。</p><br/><br/>
              <p>众筹网团队经常问自己：为什么创新精神没有得到应有的奖励？为什么一些品质拙劣的产品反而能大获“成功”？或许市场和资本只相信成王败寇。但众筹网坚信，人们与生俱来的创造力才是文化创意生产的核心。</p><br/><br/>
              <p>借助互联网的力量，众筹网给每一位创造者提供了建立个人品牌的机会。所有人都可以向世界展示自己独一无二的创意作品，讲述这个创意是如何萌发，并一步步长大。 更重要的是，优秀的创作者会因为自己的辛勤付出获得相应资金支持，吸引一批志趣相投的伙伴，一起将创意变成现实。</p><br/><br/>
              <p>当志同道合的人们相遇，创意的火花随时会被点燃。</p>
              <p>这就是众筹网存在的意义。</p>
            </div>
            </div>
          </el-col>
          <el-col :span="12">
            <img class="fi-images01" src="../assets/imgs/fs-images02.png">
            <img class="fi-images02" src="../assets/imgs/fs-images01.png">
          </el-col>
        </el-row>
      </section>
    </div>
    <!--关于我们 end -->

    <!-- 发布项目 -->
    <div class="index-warp index-submit-warp">
      <section class="container">
        <div class="py-4 text-center">
          <h5 class="mb-3">开始众筹活动</h5><br/>
          <p class="fs-16 mb-4 text-secondary">众筹网帮助您为项目筹集创意、资源、种子用户、合作伙伴，只需几个简单步骤就可以建立专案，无需任何手续费。</p>
          <br/>
          <el-button type="info" plain class="submit-btn" @click="submitProject">立即创建项目</el-button>
        </div>
      </section>
    </div>
    <!-- 发布项目 end-->
  </div>
</template>
<script>
import * as carouselsAPI from '@/api/carousels/'
import * as productsAPI from '@/api/products/'
import * as rankingAPI from '@/api/ranking/'

export default {
  data() {
    return {
      carousels: '', // 轮播图数据
      phonesList: '', // 手机商品列表
      televisionsList: '', // 小米电视商品列表
      applianceList: '', // 家电商品列表
      applianceHotList: '', //热门家电商品列表
      accessoryList: '', //配件商品列表
      accessoryHotList: '', //热门配件商品列表
      phoneShellsList: '', // 保护套商品列表
      chargersList: '', //充电器商品列表
      applianceActive: 1, // 家电当前选中的商品分类
      accessoryActive: 1, // 配件当前选中的商品分类
      start: 0,
      limit: 7
    }
  },
  watch: {
    // 家电当前选中的商品分类，响应不同的商品数据
    applianceActive: function(val) {
      // 页面初始化的时候把applianceHotList(热门家电商品列表)直接赋值给applianceList(家电商品列表)
      // 所以在切换商品列表时判断applianceHotList是否为空,为空则是第一次切换,把applianceList赋值给applianceHotList
      if (this.applianceHotList == '') {
        this.applianceHotList = this.applianceList
      }
      if (val == 1) {
        // 1为热门商品
        this.applianceList = this.applianceHotList
        return
      }
      if (val == 2) {
        // 2为电视商品
        this.applianceList = this.televisionsList
        return
      }
    },
    accessoryActive: function(val) {
      // 页面初始化的时候把accessoryHotList(热门配件商品列表)直接赋值给accessoryList(配件商品列表)
      // 所以在切换商品列表时判断accessoryHotList是否为空,为空则是第一次切换,把accessoryList赋值给accessoryHotList
      if (this.accessoryHotList == '') {
        this.accessoryHotList = this.accessoryList
      }
      if (val == 1) {
        // 1为热门商品
        this.accessoryList = this.accessoryHotList
        return
      }
      if (val == 2) {
        // 2为保护套商品
        this.accessoryList = this.phoneShellsList
        return
      }
      if (val == 3) {
        //3 为充电器商品
        this.accessoryList = this.chargersList
        return
      }
    }
  },
  activated() {
    // 获取首页数据
    this.load()
  },
  methods: {
    load() {
      carouselsAPI
          .listCarousels()
          .then(res => {
            if (res.status == 200) {
              this.carousels = res.data
            } else {
              this.notifyError('获取轮播图失败', res.msg)
            }
          })
          .catch(err => {
            this.notifyError('获取轮播图失败', err)
          })
      //获取热门家电
      rankingAPI
          .listElecRanking()
          .then(res => {
            if (res.status === 200) {
              this.applianceHotList = res.data
              this.applianceList = res.data
            } else {
              this.notifyError('获取热门家电失败')
            }
          })
          .catch(err => {
            this.notifyError('获取热门家电失败', err)
          })
      //获取热门配件
      rankingAPI
          .listAcceRanking()
          .then(res => {
            if (res.status === 200) {
              this.accessoryHotList = res.data
              this.accessoryList = res.data
            } else {
              this.notifyError('获取热门配件失败')
            }
          })
          .catch(err => {
            this.notifyError('获取热门配件失败', err)
          })
      //获取手机列表
      this.getProduct(1, 'phonesList')
      //获取电视列表
      this.getProduct(2, 'televisionsList')
      //获取保护套列表
      this.getProduct(5, 'phoneShellsList')
      //获取充电器列表
      this.getProduct(7, 'chargersList')
    },
    // 获取家电模块子组件传过来的数据
    getChildMsg(val) {
      this.applianceActive = val
    },
    // 获取配件模块子组件传过来的数据
    getChildMsg2(val) {
      this.accessoryActive = val
    },
    // 获取各类商品数据方法封装
    getProduct(categoryID, val) {
      productsAPI
          .listProducts(categoryID, this.start, this.limit)
          .then(res => {
            if (res.status === 200) {
              this[val] = res.data.items
            } else {
              this.notifyError('获取失败', res.msg)
            }
          })
          .catch(err => {
            this.notifyError('获取失败', err)
          })
    }
  },
  beforeRouteEnter(to, from, next) {
    // 添加背景色
    document
        .querySelector('body')
        .setAttribute('style', 'background-color:#ffffff')
    next()
  },
  beforeRouteLeave(to, from, next) {
    // 去除背景色
    document
        .querySelector('body')
        .setAttribute('style', 'background-color:#f5f5f5')
    next()
  }
}
</script>
<style scoped>
@import '../assets/css/index.css';

.index-warp{padding: 40px 0;}
.index-submit-warp{background-color: #F5F5F5;
  background-image: url(../assets/imgs/index-bj-02.png);
  background-repeat: no-repeat;background-position:center top;}
.submit-btn{background-color: #fff;}
</style>