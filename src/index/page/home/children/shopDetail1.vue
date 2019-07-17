<template>
  <div class="container">
    <div class="s_1">
      <ul>
        <li class="l t" @click="goBack()"></li>
        <li class="l">
            <span>已巡检商户详情</span>
        </li>
      </ul>
      <div class="hr-1"></div>
    </div>
    <div class="scrollbox">
    <ul class="formCom">
      <li>
        <label>
					<span>商户名称：</span>
          <span>{{shopData.merName}}</span>
        </label>
      </li>
			<div class="hr-2"></div>
      <li>
        <label>
					<span>商户简称：</span>
          <span>{{shopData.mAbbreviation}}</span>
        </label>
      </li>
			<div class="hr-2"></div>
      <li>
        <label>
					<span>商户编号：</span>
          <span>{{shopData.mNumber}}</span>
        </label>
      </li>
			<div class="hr-2"></div>
      <li>
        <label>
      		<span>商户地址：</span>
          <span>{{shopData.mBusAddress}}</span>
        </label>
      </li>
      <div class="hr-2"></div>
      <li>
        <label>
      		<span>商户状态：</span>
          <span>{{shopData.merStoreStatus}}</span>
          <!-- <span v-if="shopData.merStoreStatus==0">该商户位置准确且名称与营业执照一致</span>
          <span v-if="shopData.merStoreStatus==1">该地址无此商户</span>
          <span v-if="shopData.merStoreStatus==2">商户地址不详,找不到具体位置</span>
          <span v-if="shopData.merStoreStatus==3">商户异常</span> -->
        </label>
      </li>
      <!-- 商品状态为2展示这里 -->
      <!-- <li v-if="shopData.merStoreStatus==2">
        <label>
      		<span>实际地址：</span>
          <span>{{shopData.realAddress}}</span>
        </label>
      </li> -->
      <li v-if=" shopData.realAddress!='' && shopData.realAddress!=null ">
        <label>
      		<span>实际地址：</span>
          <span>{{shopData.realAddress}}</span>
        </label>
      </li>
      <!-- 商品状态为3展示这里 -->
      <li v-if="shopData.erroReason!='' && shopData.erroReason!=null">
        <label>
      		<span>异常原因：</span>
          <span>{{shopData.erroReason}}</span>
        </label>
      </li>
       <!-- 商品状态为3且异常描述不为空展示这里 -->
      <li v-if=" shopData.erroDesc!='' && shopData.erroDesc!=null ">
        <label>
      		<span>异常描述：</span>
          <span>{{shopData.erroDesc}}</span>
        </label>
      </li>
    </ul>
    <!-- 门头照片展示 -->
      <div class="photobox" v-if="shopData.imges01 && shopData.imges01.length>0">
        <div class="phototitle">门头照片：</div>
        <div class="photoimg">
          <viewer :images="shopData.imges01">
            <img v-for="(item,index) in shopData.imges01" :src="item.imageUrl" alt="" :key="item.imageUrl">
          </viewer>
        </div>
      </div>
      <!-- 和包物料照片展示 -->
      <div class="photobox" v-if="shopData.imges02 && shopData.imges02.length>0">
        <div class="phototitle">和包物料照片：</div>
        <div class="photoimg">
           <viewer :images="shopData.imges02">
             <img v-for="(item1,index) in shopData.imges02" :src="item1.imageUrl" alt="" :key="item1.imageUrl">
           </viewer>
        </div>
      </div>
      <!-- 订单交易截图展示 -->
      <div class="photobox" v-if="shopData.imges03 && shopData.imges03.length>0">
        <div class="phototitle">订单交易截图：</div>
        <div class="photoimg">
          <viewer :images="shopData.imges03">
            <img v-for="(item2,index) in shopData.imges03" :src="item2.imageUrl" alt="" :key="item2.imageUrl">
          </viewer>
        </div>
      </div>
      </div>
  </div>
</template>

<script>
import { mapState, mapMutations } from "vuex";
import axios from "@@/plugins/rsa/axios";
import {
  animationProgress,
  AppFlag
} from "@@/service/util";
import { baseUrl } from "@@/config/env";
import Scroll from "@@/components/scroll/scroll.vue";
import Cookies from 'js-cookie';

export default {
  data() {
    return {
      shopData: {},
      mNumer: '',
      couponList: [],
      isPayWrapper: false,
      scrollbar:false,
      isDisable: false,
      bgIcon: false,
      params: '',
      show: true
    };
  },
  computed: {
    ...mapState([
      "token",
      "shopParm",
      "sliderScroll",
      "latitude",
      "longitude",
      "cityname"
    ]),
    dataList() {
      if (this.showAll == false) {
        var dataList = [];
        if (this.couponList.length > 4) {
          for (var i = 0; i < 4; i++) {
            dataList.push(this.couponList[i])
          }
        } else {
          dataList = this.couponList
        }
        return dataList;
      } else {
        return this.couponList
      }
    }
  },

  mounted() {
    // 隐藏进度条
    // document.getElementById("pg").style.display="none";
  },
  created() {
    this.init();
  },
  components: {
    Scroll
  },
  methods: {
    ...mapMutations([
       'ISSHOWALERT',
        "CITYNAME1",
       'ALERTTEXTFIRST'
     ]),
    init() {
      let params =this.shopParm.merId?this.shopParm:JSON.parse(Cookies.get("item"));   //获取商铺Id
      axios.post("merchants/queryXjInfoDetails", params).then(res => {
        if (res.code === "0") {
          console.log(res.data);
          this.shopData = res.data;
          this.mNumer = res.data.mNumber;
        }else if(res.code === "-1"){
          this.$dialog("请求出现异常!");
        }else{
          this.$dialog("商品详情获取失败!");
        }
      })
    },
    clickToast() {
      this.$dialog('提交成功');
    },
    clickClose() {
      this.isPayWrapper = false;
    },
    goBusinessCheck() {
      this.$router.push("/businessCheck");
    },
    changeIscrollY(flag) {
      this.scrollY = flag;
    },
    goBack() {
      this.$router.go(-1);
      // this.$router.push("/home1");
    }
  }
};
</script>

<style lang="scss" scoped>
@import "~@@/style/mixin";
.scrollbox{
  position: absolute;
  padding-top: 3.125rem;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  box-sizing: border-box;
}
.container {
  overflow: hidden;
  min-height: 100%;
	background: #ededed;}
.s_1 {
  @include wh(100%, 3.125rem);
  background: #383d41;
  position: sticky;
  z-index: 100;
  top: 0;
  left: 0;
  .l {
    height: 100%;
    line-height: 3.125rem;
    font-size: 1.125rem;
    font-family: PingFangSC-Regular;
    color: #fff;
    padding-right: 3.5rem;
    text-align: center;
  }
  .t {
    color: #6c6c6c;
    font-size: 0.9375rem;
    height: 3.125rem;
    position: relative;
    float: left;
    text-align: left;
    background-image: url(/static/img/left_back.png);
    background-repeat: no-repeat;
    background-position: 0.375rem 50%;
    background-size: 1.4rem;
    // padding-right: 0.6rem;
    @include space();
  }
}
.formCom {
  // padding-left: 0.625rem;
  background: #fff; }
  .formCom li {
    width: 100%;
    height: 3rem;
    line-height: 3rem;
    font-family: PingFangSC-Regular;
    font-size: .875rem;
    color: #13252e;
    // border-bottom: 1px solid #e5e5e5;
    position: relative;
    overflow: hidden;
    cursor: pointer;
		.special {
			position: absolute;
			top: 1.125rem;
			right: 1rem;
			width: .9375rem;
			height: .9375rem;
		}
	}
  .formCom label {
    width: 90%;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    display: block;
    padding-left: 1.5rem;}
  .formCom input, .formCom .input {
    font-size: 0.875rem;
    line-height: 1.6rem;
    margin-top: 0.65rem;
    width: 72%;
    background: transparent;
    position: absolute;
    left: 4rem;
    top: 0;
    z-index: 2; }
  .formCom p {
    position: absolute;
    top: 0;
    width: 100%;
    padding-left: 27.5%; }
  .formCom select {
    font-size: 0.8125rem;
    height: 2.375rem;
    width: 100%;
    padding-left: 27.5%;
    background: transparent;
    position: absolute;
    top: 0;
    left: 0;
    z-index: 5;
    opacity: 0; }
  .formCom img {
    width: 1.625rem;
    height: 1.625rem;
    position: absolute;
    top: .75rem;
  }
  .formCom option {
    color: #666; }

.nullHeight {
  height: .5625rem;
  background: #f6f7f8;
}
.hr-2 {
  width: 100%;
  display: block;
  position: absolute;
  background: #f4f4f4;
  height: .0625rem;
  float: left;
}

.wrap {
  width: 95%;
  float: left;
  padding-left: 1.5rem;
    font-family: PingFangSC-Regular;
    font-size: .875rem;
  .wrap_left {
    float: left;
    line-height: 3rem;
  }
}
.wrap-text {
  display: inline-block;
  font-family: PingFangSC-Regular;
  font-size: 0.875rem;
  color: #13252e;
  padding: 0.8rem 0 0.7rem 0.3rem;
  white-space: normal;
  word-break: break-all;
  word-wrap: break-word;
  // line-height: 1.5rem;
}

.btnWarp {
  margin: 2.625rem 0.625rem 0 0.625rem;
  .subBtn {
    display: block;
    margin: 0 auto;
    width: 40%;
    color: #fff;
    text-align: center;
    font-size: 0.9375rem;
    line-height: 2.6rem;
    background-color: #5592fa;
    -webkit-border-radius: 0.3125rem;
    border-radius: 0.3125rem;
    cursor: pointer; }
  }

.payWrapper {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0,0,0,0.3);
  z-index: 999999999;
  .menuWrapper {
    position: absolute;
    bottom: 30%;
    left: 8%;
    width: 84%;
    text-align: center;
    line-height: 3rem;
    background-color: #fff;
    color: #3478f6;
    .top {
      background: url(/static/img/sys.png) no-repeat 38% 50%;
      background-size: 1rem;
    }
    .middle {
      background: url(/static/img/ewm.png) no-repeat 37.5% 50%;
      background-size: 1.4rem;
    }
    .bottom {
      color: #555;
    }
  }
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.3s;
  .menuWrapper {
    transition: all 0.3s;
  }
}
.fade-enter, .fade-leave-to {
  opacity: 0;
  .menuWrapper {
    transform: translate3d(0, 100%, 0)
  }
}

// 照片展示
.photobox{
   width: 100%;
   background: #fff;
   padding-left: 1.5rem;
   font-size: 0.86rem;
   color: #13252e;
   overflow: hidden;
   padding-top: 1rem;
  .phototitle{
    padding-bottom: 0.5rem;
  }
  .photoimg{
    width: 100%;
    height: 4.9375rem;
    overflow: hidden;
    img{
      display: block;
      float: left;
      width: 4.9375rem;
      height: 4.9375rem;
      margin-right: 1rem;
    }
  }
}
.photobox:last-child{
  padding-bottom: 1rem;
}
.viewer-toolbar{
  display: none!important;
}
</style>
