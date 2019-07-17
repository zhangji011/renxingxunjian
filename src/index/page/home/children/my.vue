<template>
  <div class="container">
    <div class="s_1">
      <ul>
        <!-- <router-link tag="li" class="l t" to="/home1"></router-link> -->
        <li class="l">
            <span>个人中心</span>
        </li>
      </ul>
      <div class="hr-1"></div>
    </div>
  		<div>
  			<div class="logoIcon"><span>{{params.substr(0,3)+" **** "+params.substr(params.length-4,params.length)}}</span></div>
  		</div>
    <ul class="formCom">
      <li class="totol" style="height:4rem;">
        <label>
          <img src="/static/img/totol.png" alt="">
  					<span>累计巡检商户：{{!params2?0:params2}}家</span>
        </label>
      </li>
      <li @click="$router.push('/home1')">
        <label>
          <img src="/static/img/ed.png" alt="">
  					<span>商户巡检</span>
          <img class="special" src="/static/img/link_button.png" alt="">
        </label>
      </li>
      <div class="nullHeight"></div>
      <li @click="$router.push('/home2')">
        <label style="padding-left: 1.2rem;">
          <img src="/static/img/yxj.png" alt="" style="width: 2.1rem;">
      			<span style="padding-left: 2.8rem;">已巡检商户</span>
          <img class="special" src="/static/img/link_button.png" alt="">
        </label>
      </li>
  			<div class="nullHeight"></div>
      <li @click="goReset()">
        <label>
          <img src="/static/img/ys.png" alt="">
  					<span>密码修改</span>
          <img class="special" src="/static/img/link_button.png" alt="">
        </label>
      </li>
  			<div class="nullHeight"></div>
      <li @click="clickLogout">
        <label>
          <img src="/static/img/exit.png" alt="">
  					<span>退出登录</span>
          <img class="special" src="/static/img/link_button.png" alt="">
        </label>
      </li>
    </ul>
  </div>
</template>

<script>
import { mapState, mapMutations } from "vuex";
import axios from "@@/plugins/rsa/axios";
import { baseUrl } from "@@/config/env"; // baseUrl
import Cookies from 'js-cookie';
export default {
  data() {
    return {
      name: '',
      params: '',
      params2: ''
    }
  },
  mounted() {
     // var id = "userid";
    // console.log(decodeURIComponent((new RegExp('[?|&]' + id + '=' + '([^&;]+?)(&|#|;|$)').exec(location.href) || [, ""])[1].replace(/\+/g, '%20')) || null)
    // if(!localStorage.getItem("mblno")){
      // console.log(localStorage.getItem(this.getUrlKey("mblno")))
    if(this.getUrlKey("mblno")!=null){

       Cookies.set("mblno",this.getUrlKey("mblno"));
      Cookies.set("totalBus",this.getUrlKey("totalBus"));
      Cookies.set("userId",this.getUrlKey("userId"));
      // localStorage.setItem("mblno",this.getUrlKey("mblno"));
      // localStorage.setItem("totalBus",this.getUrlKey("totalBus"));
      // localStorage.setItem("userId",this.getUrlKey("userId"));
    }
    // else{
    //   Cookies.set("mblno",localStorage.getItem("mblno"));
    //   Cookies.set("totalBus",localStorage.getItem("totalBus"));
    //   Cookies.set("userId",localStorage.getItem("userId"));
    // }

    this.params2=Cookies.get("params2") || 0;  //从本地获取累计巡检商户
    sessionStorage.removeItem("xjstatus");   //移除已巡检商户的状态
    this.params = Cookies.get("mblno") || '';
    Cookies.set("mblno",this.params);
	// if(this.params == '' || this.params == 'null'){
 //    this.$dialog('账号已过期,请重登!');
	// 	setTimeout(function(){
 //      // window.location.href = "http://localhost:8000/main.html#/" //本地
 //      window.location.href = "https://hbtest.letshappy.cn/rhxj/main.html" //测试使用
 //      // window.location.href = "https://find.cmpay.com:9102/hebao/rhxj/main.html" //投产使用
 //    },1000);
	// }
    this.getUserinfo();
    // this.params2 = Cookies.get("totalBus");
    // console.log(Cookies.get("mblno"));
  },
  created() {
  },

  components: {

  },

  computed: {
    ...mapState([
      "mblno"
    ])
  },

  methods: {
    getUrlKey(name){
return decodeURIComponent((new RegExp('[?|&]' + name + '=' + '([^&;]+?)(&|#|;|$)').exec(location.href) || [, ""])[1].replace(/\+/g, '%20')) || null
},
    clickLogout() {
      // console.log(Cookies.get('mblno'))
      // console.log(Cookies.get('totalBus'))
      // console.log(Cookies.get('params'))
      // console.log(Cookies.get('item'))
      // return;
      this.$weui.dialog({
        title: '退出',
        content: '是否退出当前账号',
        buttons: [{
          label: '取消',
          type: 'default',
          onClick: () => {

          }
        }, {
          label: '确定',
          type: 'primary',
          onClick: () => {
            Cookies.remove('mblno');
            Cookies.remove('totalBus');
            Cookies.remove('params');
            Cookies.remove('item');
            localStorage.removeItem("mblno");
            localStorage.removeItem("totalBus");
            localStorage.removeItem("userId");
            this.$router.push('/')
          }
        }]
      })
    },
    getUserinfo() {
      // console.log(Cookies.get("totalBus"));
      // console.log(Cookies.get("userId"))
      // return;
      axios.post("user/queryUser",{
        userId: Cookies.get("userId"),
      }).then(res => {
        console.log(res);
        if(res.code === "0") {
          if(!res.data.totalNumber){
            this.params2=0;
          }else{
            this.params2 = res.data.totalNumber;
          }
          Cookies.set("params2",this.params2);
        }
      })
    },
    goReset() {
      let params = this.$route.query.params;
      sessionStorage.params = params;
      this.$router.push({
        path: "/reset",
        query: {
            params: params
          }
      });
    },
    goBack() {
      this.$router.go(-1);
    }
  },
  activated(){
  },
  watch:{
}}
</script>

<style lang="scss" scoped>
@import "~@@/style/mixin";
.container {
  overflow: hidden;
  min-height: 100%;
	background: #ededed;}
.s_1 {
  @include wh(100%, 3.125rem);
  background: #383d41;
  position: sticky;
  z-index: 100000000;
  top: 0;
  left: 0;
  .l {
    height: 100%;
    line-height: 3.125rem;
    font-size: 1.125rem;
    font-family: PingFangSC-Regular;
    color: #fff;
    text-align: center;
  }
  .t {
    color: #6c6c6c;
    font-size: 0.9375rem;
    height: 3.125rem;
    position: relative;
    float: left;
    text-align: left;
    background-image: url(/static/img/back.png);
    background-repeat: no-repeat;
    background-position: 0.375rem 50%;
    background-size: 1.1rem;
    // padding-right: 0.6rem;
    @include space();
  }
}
.logoIcon {
	  position: relative;
  	height: 4.5rem;
    background: url(/static/img/user_logo.png) 2rem 1rem no-repeat;
    background-size: 3.5rem 3.5rem;
		span {
			position: absolute;
			top: 2.5rem;
    	left: 5.625rem;
		}
}
.formCom {
  // padding-left: 0.625rem;
  background: #fff; }
  .totol {height: 4rem;background: #ededed;}
  .formCom li {
    color: #333;
    font-size: 0.9375rem;
    height: 3rem;
    line-height: 3rem;
    // border-bottom: 1px solid #e5e5e5;
    position: relative;
    overflow: hidden;
    cursor: pointer;
		span {
			padding-left: 2.5rem;
		}
		.special {
			position: absolute;
			top: 1.125rem;
			right: 1rem;
			width: .9375rem;
			height: .9375rem;
		}
		}
  .formCom label {
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
    top: .7rem;
  }
  .formCom option {
    color: #666; }

.nullHeight {
	  height: .5625rem;
    background: #f6f7f8;
}
</style>
