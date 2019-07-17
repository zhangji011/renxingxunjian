<template>
  <div>
    <header>
      <ul>
        <!-- <router-link tag="li" class="l t" to="/"></router-link> -->
        <li class="lt ex" @click="goBack()"></li>
        <li class="l">
          <span>商户列表</span>
        </li>
      </ul>
      <div class="hr-1"></div>
    </header>
    <section class="s_1">
      <ul>
        <!-- <li class="l t" @click="getClickCity()"> -->
        <li class="l t" @click="getClickCity()">
          <span v-show="showCity">{{cityName1.split("市")[0]}}</span>
          <span v-show="showCitys">{{county}}</span>
          <!-- {{cityName2}} -->
        </li>
        <li class="l i" @click="done()">
          <button><span class="icon"><img src="/static/img/1-20.png"></span>请输入商户名称</button>
        </li>
      </ul>
    </section>

    <div class="home">
      <scroll
        :data1 ="data1"
        :data = "shopList"
        :scrollbar='tabScrollbar'
        :pullDownRefresh='pullDownRefresh'
        :refreshNow = 'refreshNow'
        :scrollY = "scrollYOther"
        :pullUpLoad= "pullUpLoad_near"
        @pullingDown="onPullingDown"
        @pullingUp="onPullingUp"
        scrollbar=true
        >
        <div class="content">
          <div class="nullHeight"></div>
          <section class="s_5 s">
            <near1
              :data1 = "data1"
              :titleParm = "titleParm"
              :latitude = 'latitude'
              :longitude = 'longitude'
              :shopList="shopList"
              :data = "shopList"
              @aginEnter = "aginEnter"
              @goDetail="goDetail"
              >
            </near1>
          </section>
        </div>
      </scroll>
      </div>
      <div v-show="isStatus" class="foot_login fx" id="foot_login fx">
        <ul>
          <li class="login_l">
            <img src="/static/img/login_logo.png">
          </li>
          <li class="login_m">
            登录查看更多优惠
          </li>
          <li class="login_r">
            <span @click="goLogin()">立即登录</span>
          </li>
        </ul>
      </div>
  </div>
</template>

<script>
import { mapState, mapMutations } from "vuex";
import axios from "@@/plugins/rsa/axios";
import Loading from "@@/components/loading/loading.vue";
import {
  fetchPoints,
  // GetDistance,
  isHebaoApp,
  shareNow,
  setLItem,
  animationProgress,
  getLItem,
  formatDate_1
} from "@@/service/util";
import { baseUrl } from "@@/config/env"; // baseUrl
import Scroll from "@@/components/scroll/scroll.vue";
import Near1 from "./near1.vue";
import Cookies from 'js-cookie'
import Bus from './gongtong.js'

export default {
  data() {
    return {
      pullUpLoad: false,
      pullUpLoad_near: true,
      data1: false,
      isStatus: false,
      showCity: true,
      showCitys: false,
      titleParm: "",
      loadText: "",
      county: "",
      banner: "/static/mine_banner.png",
      icon: require("@@/images/mine/help_other-pressed.png"),
      product: [],
      listHeight: [],
      scrollY: true,
      topCat: false,
      scrollbar: false,
      slider_top_click: true,
      slider_middle_click: true,
      bounce: false,
      defaultIcon: 'this.src="' + "/static/img/error.png" + '"',
      tabAutoPlay: false,
      tabLoop: false,
      tabScrollbar: false,
      showDot: true,
      refreshNow:true,
      dots: ["附近", "推荐", "世界杯专区"], //['附近','推荐','世界杯专区','咨询'],
      isError: true,
      loopX: true,
      shopList: [],
      limit: "",
      creditResult: "",
      isShowAlert: false,
      alertTextFirst: "",
      alertTextSecond: "",
      btnText: "",
      isAdmittance: false,
      home: "",
      foodsScroll: "",
      residue: 0,
      flag: false,
      shopListFlag: false,
      CURRENTPAGE: 0, // 页码
      PAGNUM: 30,
      refTime: "",
      baseImg: baseUrl.img,
      totalInit: 0,
      endX: 0,
      sliderIndex: 0,
      startX: 0,
      scrollY: true,
      scrollYOther: true,
      startY: 0,
      threshold: 0.2,
      cityName1: window.CITYNAME || sessionStorage.getItem('cityItem') || "定位中",
      cityName2: window.CITYNAME2,
      pullDownRefresh: {
        threshold: 120,
        stop: 60
      }
    };
  },
  computed: {},

  mounted() {
    // let mblno = Cookies.get("mblno") || '';
    // if(mblno==''){
    //   this.$dialog('账号已过期,请重登!');
    // 	setTimeout(function(){
    //     window.location.href = "http://localhost:8000/main.html#/" //本地
    //     // window.location.href = "https://hbtest.letshappy.cn/rhxj/main.html" //测试使用
    //     // window.location.href = "https://find.cmpay.com:9102/hebao/rhxj/main.html" //投产使用
    //   },1000);
    // }
    // console.log(Cookies.get("mblno"));
    sessionStorage.removeItem("item1");
    sessionStorage.removeItem("item2");
    // this.l = sessionStorage.getItem("item1") || "";
    // this.s = sessionStorage.getItem("item2") || "";
    // if(this.l!="" && this.s !="" ){
    //    return;
    // }
    var vm = this
    // 用$on事件来接收参数
    Bus.$on('val', (data) => {
      vm.cityName1 = data
    })
    Cookies.set("href",window.location.href)
    try {
      console.log("okokok",this.countyParm);
      fetchPoints(
        "home1",
        "event_3",
        this.token.productNo,
        "进入附近商户",
        this.token.session.replace(/\+/g, "%2B")
      );
    } catch (e) {}
    if (!window.LATITUDE) {
      // this.aginEnter();
    } else {
      this.init();
    }
  },
  created() {
  },

  components: {
    Loading,
    Scroll,
    Near1
    // Recommended,
    // Consulting,
    // GoodThing
    // vueLoading
    // SlideRender
  },

  computed: {
    ...mapState([
      "slider",
      "slider1",
      "slider2",
      "products",
      "showLoading",
      "token",
      "countyParm",
      "tokenstatus",
      "latitude",
      "longitude",
      "cityname",
      "sliderScroll",
      "slideIndex",
      "openAndClose"
    ])
  },

  methods: {
    ...mapMutations([
      "SLIDER1", // 我的页面banner图
      "PRODUCTS",
      "SHOWLOADING",
      "CITYNAME1",
      "CITYNAME2",
      // "CITYCOUNTY",
      "OPENANDCLOSE"
    ]),
    scrollListen(pos) {
      console.log("home");
      console.log(pos);
      // setTimeout(()=>{

      // })
      // 处理滑动
      // if(Math.abs(pos.y)>280){
      //   this.scrollYOther = true;
      //   this.scrollY = false
      //   // this.startY = pos.y
      // }else if(Math.abs(pos.y)>0){
      //   console.log(99999)
      //   this.scrollYOther = false;
      //   // this.scrollY = true
      // }
      if (Math.abs(pos.y) > 320) {
        // this.scrollY = false
        // this.scrollYOther = true;
        // this.startY = -321
        this.OPENANDCLOSE(false);
        this.topCat = true;
      } else {
        this.topCat = false;
        this.OPENANDCLOSE(true);
      }
    },
    changeIscrollY(flag) {
      this.scrollY = flag;
    },
    goToPage(index) {
      this.sliderIndex = index;
    },
    getClickCity() {// 级联选择器
          this.$weui.picker([
              {
                label:'山东',
                children: [{
                    children: [{
                        label: '淄川区'
                    }, {
                        label: '张店区'
                    }, {
                        label: '高青县'
                    }, {
                        label: '桓台县'
                    }, {
                        label: '博山区'
                    }, {
                        label: '临淄区'
                    }, {
                        label: '沂源县'
                    }, {
                        label: '周村区'
                    }],
                    label: '淄博'
                }, {
                    children: [{
                        label: '山亭区'
                    }, {
                        label: '市中区'
                    }, {
                        label: '峄城区'
                    }, {
                        label: '薛城区'
                    }, {
                        label: '滕州市'
                    }, {
                        label: '台儿庄区'
                    }],
                    label: '枣庄'
                }, {
                    children: [{
                        label: '芝罘区'
                    }, {
                        label: '莱阳市'
                    }, {
                        label: '海阳市'
                    }, {
                        label: '莱州市'
                    }, {
                        label: '福山区'
                    }, {
                        label: '栖霞市'
                    }, {
                        label: '龙口市'
                    }, {
                        label: '招远市'
                    }, {
                        label: '牟平区'
                    }, {
                        label: '长岛县'
                    }, {
                        label: '蓬莱市'
                    }, {
                        label: '莱山区'
                    }],
                    label: '烟台'
                }, {
                    children: [{
                        label: '潍城区'
                    }, {
                        label: '奎文区'
                    }, {
                        label: '昌乐县'
                    }, {
                        label: '昌邑市'
                    }, {
                        label: '诸城市'
                    }, {
                        label: '安丘市'
                    }, {
                        label: '寒亭区'
                    }, {
                        label: '高密市'
                    }, {
                        label: '坊子区'
                    }, {
                        label: '青州市'
                    }, {
                        label: '寿光市'
                    }, {
                        label: '临朐县'
                    }],
                    label: '潍坊'
                }, {
                    children: [{
                        label: '环翠区'
                    }, {
                        label: '文登区'
                    }, {
                        label: '乳山市'
                    }, {
                        label: '荣成市'
                    }],
                    label: '威海'
                }, {
                    children: [{
                        label: '泰山区'
                    }, {
                        label: '岱岳区'
                    }, {
                        label: '肥城市'
                    }, {
                        label: '新泰市'
                    }, {
                        label: '东平县'
                    }, {
                        label: '宁阳县'
                    }],
                    label: '泰安'
                }, {
                    children: [{
                        label: '东港区'
                    }, {
                        label: '莒县'
                    }, {
                        label: '五莲县'
                    }, {
                        label: '岚山区'
                    }],
                    label: '日照'
                }, {
                    children: [{
                        label: '李沧区'
                    }, {
                        label: '市北区'
                    }, {
                        label: '城阳区'
                    }, {
                        label: '平度市'
                    }, {
                        label: '黄岛区'
                    }, {
                        label: '崂山区'
                    }, {
                        label: '莱西市'
                    }, {
                        label: '即墨市'
                    }, {
                        label: '胶州市'
                    }, {
                        label: '市南区'
                    }],
                    label: '青岛'
                }, {
                    children: [{
                        label: '河东区'
                    }, {
                        label: '临沭县'
                    }, {
                        label: '兰山区'
                    }, {
                        label: '罗庄区'
                    }, {
                        label: '郯城县'
                    }, {
                        label: '蒙阴县'
                    }, {
                        label: '沂南县'
                    }, {
                        label: '莒南县'
                    }, {
                        label: '兰陵县'
                    }, {
                        label: '平邑县'
                    }, {
                        label: '费县'
                    }, {
                        label: '沂水县'
                    }],
                    label: '临沂'
                }, {
                    children: [{
                        label: '东昌府区'
                    }, {
                        label: '茌平县'
                    }, {
                        label: '莘县'
                    }, {
                        label: '阳谷县'
                    }, {
                        label: '冠县'
                    }, {
                        label: '临清市'
                    }, {
                        label: '高唐县'
                    }, {
                        label: '东阿县'
                    }],
                    label: '聊城'
                }, {
                    children: [{
                        label: '莱城区'
                    }, {
                        label: '钢城区'
                    }],
                    label: '莱芜'
                }, {
                    children: [{
                        label: '任城区'
                    }, {
                        label: '泗水县'
                    }, {
                        label: '微山县'
                    }, {
                        label: '汶上县'
                    }, {
                        label: '兖州区'
                    }, {
                        label: '梁山县'
                    }, {
                        label: '鱼台县'
                    }, {
                        label: '邹城市'
                    }, {
                        label: '曲阜市'
                    }, {
                        label: '嘉祥县'
                    }, {
                        label: '金乡县'
                    }],
                    label: '济宁'
                }, {
                    children: [{
                        label: '市中区'
                    }, {
                        label: '历下区'
                    }, {
                        label: '长清区'
                    }, {
                        label: '章丘市'
                    }, {
                        label: '历城区'
                    }, {
                        label: '济阳县'
                    }, {
                        label: '平阴县'
                    }, {
                        label: '槐荫区'
                    }, {
                        label: '商河县'
                    }, {
                        label: '天桥区'
                    }, {
                        label: '莱城区'
                    }],
                    label: '济南'
                }, {
                    children: [{
                        label: '牡丹区'
                    }, {
                        label: '东明县'
                    }, {
                        label: '郓城县'
                    }, {
                        label: '曹县'
                    }, {
                        label: '鄄城县'
                    }, {
                        label: '定陶区'
                    }, {
                        label: '成武县'
                    }, {
                        label: '单县'
                    }, {
                        label: '巨野县'
                    }],
                    label: '菏泽'
                }, {
                    children: [{
                        label: '东营区'
                    }, {
                        label: '利津县'
                    }, {
                        label: '广饶县'
                    }, {
                        label: '河口区'
                    }, {
                        label: '垦利区'
                    }],
                    label: '东营'
                }, {
                    children: [{
                        label: '德城区'
                    }, {
                        label: '禹城市'
                    }, {
                        label: '临邑县'
                    }, {
                        label: '乐陵市'
                    }, {
                        label: '宁津县'
                    }, {
                        label: '夏津县'
                    }, {
                        label: '武城县'
                    }, {
                        label: '平原县'
                    }, {
                        label: '齐河县'
                    }, {
                        label: '陵城区'
                    }, {
                        label: '庆云县'
                    }],
                    label: '德州'
                }, {
                    children: [{
                        label: '滨城区'
                    }, {
                        label: '沾化区'
                    }, {
                        label: '博兴县'
                    }, {
                        label: '邹平县'
                    }, {
                        label: '无棣县'
                    }, {
                        label: '阳信县'
                    }, {
                        label: '惠民县'
                    }],
                    label: '滨州'
                }]
                },{
                label:'福建',
                children: [{
                    children: [{
                        label: '龙海市'
                    }, {
                        label: '漳浦县'
                    }, {
                        label: '芗城区'
                    }, {
                        label: '龙文区'
                    }, {
                        label: '云霄县'
                    }, {
                        label: '东山县'
                    }, {
                        label: '南靖县'
                    }, {
                        label: '华安县'
                    }, {
                        label: '诏安县'
                    }, {
                        label: '长泰县'
                    }, {
                        label: '平和县'
                    }, {
                        label: '仓山区'
                    }],
                    label: '漳州'
                }, {
                    children: [{
                        label: '湖里区'
                    }, {
                        label: '同安区'
                    }, {
                        label: '翔安区'
                    }, {
                        label: '海沧区'
                    }, {
                        label: '集美区'
                    }, {
                        label: '思明区'
                    }],
                    label: '厦门'
                }, {
                    children: [{
                        label: '永安市'
                    }, {
                        label: '宁化县'
                    }, {
                        label: '沙县'
                    }, {
                        label: '将乐县'
                    }, {
                        label: '建宁县'
                    }, {
                        label: '尤溪县'
                    }, {
                        label: '梅列区'
                    }, {
                        label: '清流县'
                    }, {
                        label: '泰宁县'
                    }, {
                        label: '仓山区'
                    }, {
                        label: '大田县'
                    }, {
                        label: '明溪县'
                    }, {
                        label: '三元区'
                    }],
                    label: '三明'
                }, {
                    children: [{
                        label: '南安市'
                    }, {
                        label: '丰泽区'
                    }, {
                        label: '晋江市'
                    }, {
                        label: '石狮市'
                    }, {
                        label: '永春县'
                    }, {
                        label: '惠安县'
                    }, {
                        label: '泉港区'
                    }, {
                        label: '德化县'
                    }, {
                        label: '安溪县'
                    }, {
                        label: '鲤城区'
                    }, {
                        label: '洛江区'
                    }],
                    label: '泉州'
                }, {
                    children: [{
                        label: '仙游县'
                    }, {
                        label: '涵江区'
                    }, {
                        label: '秀屿区'
                    }, {
                        label: '荔城区'
                    }, {
                        label: '城厢区'
                    }],
                    label: '莆田'
                }, {
                    children: [{
                        label: '蕉城区'
                    }, {
                        label: '福鼎市'
                    }, {
                        label: '福安市'
                    }, {
                        label: '古田县'
                    }, {
                        label: '柘荣县'
                    }, {
                        label: '周宁县'
                    }, {
                        label: '寿宁县'
                    }, {
                        label: '霞浦县'
                    }, {
                        label: '屏南县'
                    }],
                    label: '宁德'
                }, {
                    children: [{
                        label: '建瓯市'
                    }, {
                        label: '顺昌县'
                    }, {
                        label: '建阳区'
                    }, {
                        label: '政和县'
                    }, {
                        label: '松溪县'
                    }, {
                        label: '武夷山市'
                    }, {
                        label: '邵武市'
                    }, {
                        label: '浦城县'
                    }, {
                        label: '光泽县'
                    }, {
                        label: '延平区'
                    }],
                    label: '南平'
                }, {
                    children: [{
                        label: '武平县'
                    }, {
                        label: '漳平市'
                    }, {
                        label: '新罗区'
                    }, {
                        label: '连城县'
                    }, {
                        label: '长汀县'
                    }, {
                        label: '永定区'
                    }, {
                        label: '上杭县'
                    }],
                    label: '龙岩'
                }, {
                    children: [{
                        label: '鼓楼区'
                    }, {
                        label: '长乐市'
                    }, {
                        label: '晋安区'
                    }, {
                        label: '台江区'
                    }, {
                        label: '闽侯县'
                    }, {
                        label: '福清市'
                    }, {
                        label: '连江县'
                    }, {
                        label: '仓山区'
                    }, {
                        label: '永泰县'
                    }, {
                        label: '马尾区'
                    }, {
                        label: '平潭县'
                    }, {
                        label: '罗源县'
                    }, {
                        label: '闽清县'
                    }],
                    label: '福州'
                }]
                },
                {
                    label: '河南',
                children: [{
                    children: [{
                        label: '驿城区'
                    }],
                    label: '驻马店'
                }, {
                    children: [{
                        label: '金水区'
                    }, {
                        label: '二七区'
                    }, {
                        label: '中原区'
                    }, {
                        label: '新郑市'
                    }, {
                        label: '管城回族区'
                    }],
                    label: '郑州'
                }, {
                    children: [{
                        label: '魏都区'
                    }],
                    label: '许昌'
                }, {
                    children: [{
                        label: '长垣县'
                    }, {
                        label: '红旗区'
                    }, {
                        label: '牧野区'
                    }, {
                        label: '凤泉区'
                    }, {
                        label: '封丘县'
                    }, {
                        label: '卫滨区'
                    }],
                    label: '新乡'
                }, {
                    children: [{
                        label: '梁园区'
                    }],
                    label: '商丘'
                }, {
                    children: [{
                        label: '宛城区'
                    }, {
                        label: '卧龙区'
                    }],
                    label: '南阳'
                }, {
                    children: [{
                        label: '源汇区'
                    }],
                    label: '漯河'
                }, {
                    children: [{
                        label: '西工区'
                    }, {
                        label: '新安县'
                    }, {
                        label: '栾川县'
                    }, {
                        label: '洛龙区'
                    }, {
                        label: '涧西区'
                    }, {
                        label: '偃师市'
                    }],
                    label: '洛阳'
                }, {
                    children: [{
                        label: '鼓楼区'
                    }],
                    label: '开封'
                }, {
                    children: [{
                        label: '解放区'
                    }, {
                        label: '山阳区'
                    }],
                    label: '焦作'
                }, {
                    children: [{
                        label: '济源市'
                    }],
                    label: '济源'
                }, {
                    children: [{
                        label: '淇县'
                    }, {
                        label: '淇滨区'
                    }],
                    label: '鹤壁'
                }, {
                    children: [{
                        label: '文峰区'
                    }],
                    label: '安阳'
                }]
                },
                {
                label: '河北',
                children: [{
                    children: [{
                        label: '桥东区'
                    }, {
                        label: '蔚县'
                    }, {
                        label: '沽源县'
                    }, {
                        label: '张北县'
                    }, {
                        label: '宣化区'
                    }, {
                        label: '桥西区'
                    }, {
                        label: '怀来县'
                    }, {
                        label: '下花园区'
                    }, {
                        label: '赤城县'
                    }, {
                        label: '康保县'
                    }, {
                        label: '涿鹿县'
                    }, {
                        label: '怀安县'
                    }, {
                        label: '尚义县'
                    }, {
                        label: '崇礼区'
                    }, {
                        label: '万全区'
                    }, {
                        label: '阳原县'
                    }],
                    label: '张家口'
                }, {
                    children: [{
                        label: '桥西区'
                    }, {
                        label: '临城县'
                    }, {
                        label: '任县'
                    }, {
                        label: '宁晋县'
                    }, {
                        label: '平乡县'
                    }, {
                        label: '沙河市'
                    }, {
                        label: '清河县'
                    }, {
                        label: '临西县'
                    }, {
                        label: '威县'
                    }, {
                        label: '新河县'
                    }, {
                        label: '内丘县'
                    }, {
                        label: '广宗县'
                    }, {
                        label: '柏乡县'
                    }, {
                        label: '桥东区'
                    }, {
                        label: '巨鹿县'
                    }, {
                        label: '南和县'
                    }, {
                        label: '邢台县'
                    }, {
                        label: '隆尧县'
                    }, {
                        label: '南宫市'
                    }],
                    label: '邢台'
                }, {
                    children: [{
                        label: '滦南县'
                    }, {
                        label: '路北区'
                    }, {
                        label: '古冶区'
                    }, {
                        label: '丰润区'
                    }, {
                        label: '丰南区'
                    }, {
                        label: '迁安市'
                    }, {
                        label: '滦县'
                    }, {
                        label: '开平区'
                    }],
                    label: '唐山'
                }, {
                    children: [{
                        label: '正定县'
                    }, {
                        label: '井陉矿区'
                    }, {
                        label: '裕华区'
                    }, {
                        label: '元氏县'
                    }, {
                        label: '藁城区'
                    }, {
                        label: '新乐市'
                    }, {
                        label: '栾城区'
                    }, {
                        label: '行唐县'
                    }, {
                        label: '赞皇县'
                    }, {
                        label: '晋州市'
                    }, {
                        label: '井陉县'
                    }, {
                        label: '新华区'
                    }, {
                        label: '长安区'
                    }, {
                        label: '桥西区'
                    }, {
                        label: '高邑县'
                    }, {
                        label: '无极县'
                    }, {
                        label: '深泽县'
                    }, {
                        label: '灵寿县'
                    }, {
                        label: '赵县'
                    }, {
                        label: '鹿泉区'
                    }, {
                        label: '辛集市'
                    }, {
                        label: '平山县'
                    }],
                    label: '石家庄'
                }, {
                    children: [{
                        label: '海港区'
                    }, {
                        label: '抚宁区'
                    }, {
                        label: '青龙满族自治县'
                    }, {
                        label: '山海关区'
                    }, {
                        label: '北戴河区'
                    }, {
                        label: '卢龙县'
                    }, {
                        label: '昌黎县'
                    }],
                    label: '秦皇岛'
                }, {
                    children: [{
                        label: '三河市'
                    }, {
                        label: '霸州市'
                    }, {
                        label: '香河县'
                    }, {
                        label: '固安县'
                    }, {
                        label: '广阳区'
                    }, {
                        label: '大厂回族自治县'
                    }, {
                        label: '安次区'
                    }, {
                        label: '大城县'
                    }, {
                        label: '永清县'
                    }, {
                        label: '文安县'
                    }],
                    label: '廊坊'
                }, {
                    children: [{
                        label: '深州市'
                    }, {
                        label: '阜城县'
                    }, {
                        label: '枣强县'
                    }, {
                        label: '景县'
                    }, {
                        label: '武邑县'
                    }, {
                        label: '桃城区'
                    }, {
                        label: '冀州区'
                    }, {
                        label: '故城县'
                    }, {
                        label: '安平县'
                    }, {
                        label: '武强县'
                    }],
                    label: '衡水'
                }, {
                    children: [{
                        label: '复兴区'
                    }, {
                        label: '丛台区'
                    }, {
                        label: '峰峰矿区'
                    }, {
                        label: '临漳县'
                    }, {
                        label: '魏县'
                    }, {
                        label: '邯山区'
                    }, {
                        label: '曲周县'
                    }, {
                        label: '永年县'
                    }, {
                        label: '成安县'
                    }, {
                        label: '鸡泽县'
                    }, {
                        label: '广平县'
                    }, {
                        label: '涉县'
                    }, {
                        label: '邱县'
                    }, {
                        label: '磁县'
                    }, {
                        label: '武安市'
                    }, {
                        label: '肥乡县'
                    }, {
                        label: '大名县'
                    }, {
                        label: '馆陶县'
                    }, {
                        label: '邯郸县'
                    }],
                    label: '邯郸'
                }, {
                    children: [{
                        label: '鹰手营子矿区'
                    }, {
                        label: '双桥区'
                    }, {
                        label: '丰宁满族自治县'
                    }, {
                        label: '宽城满族自治县'
                    }, {
                        label: '承德县'
                    }, {
                        label: '隆化县'
                    }, {
                        label: '围场满族蒙古族自治县'
                    }, {
                        label: '滦平县'
                    }, {
                        label: '双滦区'
                    }, {
                        label: '兴隆县'
                    }, {
                        label: '平泉县'
                    }],
                    label: '承德'
                }, {
                    children: [{
                        label: '孟村回族自治县'
                    }, {
                        label: '献县'
                    }, {
                        label: '东光县'
                    }, {
                        label: '任丘市'
                    }, {
                        label: '河间市'
                    }, {
                        label: '泊头市'
                    }, {
                        label: '运河区'
                    }, {
                        label: '吴桥县'
                    }, {
                        label: '青县'
                    }, {
                        label: '新华区'
                    }, {
                        label: '黄骅市'
                    }, {
                        label: '沧县'
                    }, {
                        label: '肃宁县'
                    }],
                    label: '沧州'
                }, {
                    children: [{
                        label: '高碑店市'
                    }, {
                        label: '涿州市'
                    }, {
                        label: '雄县'
                    }, {
                        label: '曲阳县'
                    }, {
                        label: '安新县'
                    }, {
                        label: '莲池区'
                    }, {
                        label: '唐县'
                    }, {
                        label: '高阳县'
                    }, {
                        label: '徐水区'
                    }, {
                        label: '满城区'
                    }, {
                        label: '望都县'
                    }, {
                        label: '武安市'
                    }, {
                        label: '清苑区'
                    }, {
                        label: '井陉县'
                    }, {
                        label: '竞秀区'
                    }, {
                        label: '定州市'
                    }, {
                        label: '顺平县'
                    }],
                    label: '保定'
                }]
                },
                {
                label: '重庆',
                children: [{
                    children: [{
                        label: '梁平县'
                    }, {
                        label: '大渡口区'
                    }, {
                        label: '沙坪坝区'
                    }, {
                        label: '潼南区'
                    }, {
                        label: '北碚区'
                    }, {
                        label: '璧山区'
                    }, {
                        label: '南岸区'
                    }, {
                        label: '云阳县'
                    }, {
                        label: '大足区'
                    }, {
                        label: '江北区'
                    }, {
                        label: '江津区'
                    }, {
                        label: '铜梁区'
                    }, {
                        label: '涪陵区'
                    }, {
                        label: '渝北区'
                    }, {
                        label: '酉阳土家族苗族自治县'
                    }, {
                        label: '巴南区'
                    }, {
                        label: '永川区'
                    }, {
                        label: '南川区'
                    }, {
                        label: '秀山土家族苗族自治县'
                    }, {
                        label: '渝中区'
                    }, {
                        label: '石柱土家族自治县'
                    }, {
                        label: '万州区'
                    }, {
                        label: '彭水苗族土家族自治县'
                    }, {
                        label: '綦江区'
                    }, {
                        label: '合川区'
                    }, {
                        label: '长寿区'
                    }, {
                        label: '奉节县'
                    }, {
                        label: '荣昌区'
                    }, {
                        label: '丰都县'
                    }, {
                        label: '忠县'
                    }, {
                        label: '九龙坡区'
                    }, {
                        label: '巫山县'
                    }, {
                        label: '城口县'
                    }, {
                        label: '垫江县'
                    }, {
                        label: '开州区'
                    }, {
                        label: '武隆县'
                    }, {
                        label: '巫溪县'
                    }, {
                        label: '黔江区'
                    }, {
                        label: '重庆郊县'
                    }],
                    label: '重庆'
                }, {
                    children: [{
                        label: '万州区'
                    }],
                    label: '万州'
                }]
                },
                {
                label: '湖南',
                children: [{
                    children: [{
                        label: '醴陵市'
                    }, {
                        label: '天元区'
                    }, {
                        label: '攸县'
                    }, {
                        label: '茶陵县'
                    }, {
                        label: '芦淞区'
                    }, {
                        label: '石峰区'
                    }, {
                        label: '株洲县'
                    }, {
                        label: '荷塘区'
                    }, {
                        label: '炎陵县'
                    }],
                    label: '株洲'
                }, {
                    children: [{
                        label: '岳麓区'
                    }, {
                        label: '芙蓉区'
                    }, {
                        label: '长沙县'
                    }, {
                        label: '天心区'
                    }, {
                        label: '开福区'
                    }, {
                        label: '雨花区'
                    }, {
                        label: '望城区'
                    }, {
                        label: '浏阳市'
                    }, {
                        label: '宁乡县'
                    }, {
                        label: '武陵区'
                    }],
                    label: '长沙'
                }, {
                    children: [{
                        label: '永定区'
                    }, {
                        label: '武陵源区'
                    }, {
                        label: '慈利县'
                    }, {
                        label: '桑植县'
                    }],
                    label: '张家界'
                }, {
                    children: [{
                        label: '岳阳楼区'
                    }, {
                        label: '平江县'
                    }, {
                        label: '临湘市'
                    }, {
                        label: '华容县'
                    }, {
                        label: '湘阴县'
                    }, {
                        label: '云溪区'
                    }, {
                        label: '岳阳县'
                    }, {
                        label: '君山区'
                    }, {
                        label: '汨罗市'
                    }],
                    label: '岳阳'
                }, {
                    children: [{
                        label: '冷水滩区'
                    }, {
                        label: '双牌县'
                    }, {
                        label: '宁远县'
                    }, {
                        label: '江永县'
                    }, {
                        label: '江华瑶族自治县'
                    }, {
                        label: '道县'
                    }, {
                        label: '蓝山县'
                    }, {
                        label: '新田县'
                    }, {
                        label: '东安县'
                    }, {
                        label: '祁阳县'
                    }, {
                        label: '零陵区'
                    }],
                    label: '永州'
                }, {
                    children: [{
                        label: '赫山区'
                    }, {
                        label: '沅江市'
                    }, {
                        label: '资阳区'
                    }, {
                        label: '桃江县'
                    }, {
                        label: '南县'
                    }, {
                        label: '安化县'
                    }],
                    label: '益阳'
                }, {
                    children: [{
                        label: '岳塘区'
                    }, {
                        label: '湘乡市'
                    }, {
                        label: '韶山市'
                    }, {
                        label: '雨湖区'
                    }, {
                        label: '湘潭县'
                    }],
                    label: '湘潭'
                }, {
                    children: [{
                        label: '双清区'
                    }, {
                        label: '大祥区'
                    }, {
                        label: '城步苗族自治县'
                    }, {
                        label: '洞口县'
                    }, {
                        label: '北塔区'
                    }, {
                        label: '隆回县'
                    }, {
                        label: '邵东县'
                    }, {
                        label: '邵阳县'
                    }, {
                        label: '绥宁县'
                    }, {
                        label: '武冈市'
                    }, {
                        label: '新邵县'
                    }, {
                        label: '新宁县'
                    }],
                    label: '邵阳'
                }, {
                    children: [{
                        label: '涟源市'
                    }, {
                        label: '新化县'
                    }, {
                        label: '娄星区'
                    }, {
                        label: '双峰县'
                    }, {
                        label: '冷水江市'
                    }],
                    label: '娄底'
                }, {
                    children: [{
                        label: '吉首市'
                    }, {
                        label: '凤凰县'
                    }, {
                        label: '古丈县'
                    }, {
                        label: '龙山县'
                    }, {
                        label: '花垣县'
                    }, {
                        label: '永顺县'
                    }, {
                        label: '泸溪县'
                    }, {
                        label: '开福区'
                    }, {
                        label: '保靖县'
                    }],
                    label: '吉首'
                }, {
                    children: [{
                        label: '新晃侗族自治县'
                    }, {
                        label: '沅陵县'
                    }, {
                        label: '鹤城区'
                    }, {
                        label: '会同县'
                    }, {
                        label: '中方县'
                    }, {
                        label: '芷江侗族自治县'
                    }, {
                        label: '洪江市'
                    }, {
                        label: '靖州苗族侗族自治县'
                    }, {
                        label: '溆浦县'
                    }, {
                        label: '通道侗族自治县'
                    }, {
                        label: '麻阳苗族自治县'
                    }],
                    label: '怀化'
                }, {
                    children: [{
                        label: '石鼓区'
                    }, {
                        label: '蒸湘区'
                    }, {
                        label: '雁峰区'
                    }, {
                        label: '珠晖区'
                    }, {
                        label: '衡山县'
                    }, {
                        label: '衡阳县'
                    }, {
                        label: '耒阳市'
                    }, {
                        label: '衡东县'
                    }, {
                        label: '常宁市'
                    }, {
                        label: '衡南县'
                    }, {
                        label: '祁东县'
                    }, {
                        label: '南岳区'
                    }],
                    label: '衡阳'
                }, {
                    children: [{
                        label: '桂东县'
                    }, {
                        label: '桂阳县'
                    }, {
                        label: '永兴县'
                    }, {
                        label: '北湖区'
                    }, {
                        label: '资兴市'
                    }, {
                        label: '苏仙区'
                    }, {
                        label: '嘉禾县'
                    }, {
                        label: '临武县'
                    }, {
                        label: '安仁县'
                    }, {
                        label: '汝城县'
                    }, {
                        label: '宜章县'
                    }],
                    label: '郴州'
                }, {
                    children: [{
                        label: '武陵区'
                    }, {
                        label: '石门县'
                    }, {
                        label: '临澧县'
                    }, {
                        label: '汉寿县'
                    }, {
                        label: '澧县'
                    }, {
                        label: '津市市'
                    }, {
                        label: '安乡县'
                    }, {
                        label: '鼎城区'
                    }, {
                        label: '桃源县'
                    }],
                    label: '常德'
                }]
                },
                {
                label: '湖北',
                children: [{
                    children: [{
                        label: '伍家岗区'
                    }],
                    label: '宜昌'
                }, {
                    children: [{
                        label: '孝昌县'
                    }],
                    label: '孝感'
                }, {
                    children: [{
                        label: '咸安区'
                    }],
                    label: '咸宁'
                }, {
                    children: [{
                        label: '江汉区'
                    }, {
                        label: '洪山区'
                    }, {
                        label: '新洲区'
                    }, {
                        label: '江岸区'
                    }, {
                        label: '江夏区'
                    }],
                    label: '武汉'
                }, {
                    children: [{
                        label: '石首市'
                    }, {
                        label: '洪湖市'
                    }, {
                        label: '荆州区'
                    }, {
                        label: '松滋市'
                    }, {
                        label: '公安县'
                    }, {
                        label: '沙市区'
                    }, {
                        label: '监利县'
                    }, {
                        label: '江陵县'
                    }],
                    label: '荆州'
                }, {
                    children: [{
                        label: '下陆区'
                    }, {
                        label: '大冶市'
                    }, {
                        label: '阳新县'
                    }, {
                        label: '黄石港区'
                    }],
                    label: '黄石'
                }, {
                    children: [{
                        label: '团风县'
                    }],
                    label: '黄冈'
                }]
                },
                {
                    label: '江西',
                children: [{
                    children: [{
                        label: '月湖区'
                    }, {
                        label: '贵溪市'
                    }, {
                        label: '余江县'
                    }],
                    label: '鹰潭'
                }, {
                    children: [{
                        label: '丰城市'
                    }, {
                        label: '上高县'
                    }, {
                        label: '袁州区'
                    }, {
                        label: '靖安县'
                    }, {
                        label: '奉新县'
                    }, {
                        label: '铜鼓县'
                    }, {
                        label: '樟树市'
                    }, {
                        label: '宜丰县'
                    }, {
                        label: '万载县'
                    }, {
                        label: '高安市'
                    }],
                    label: '宜春'
                }, {
                    children: [{
                        label: '渝水区'
                    }, {
                        label: '分宜县'
                    }],
                    label: '新余'
                }, {
                    children: [{
                        label: '鄱阳县'
                    }, {
                        label: '玉山县'
                    }, {
                        label: '广丰区'
                    }, {
                        label: '信州区'
                    }, {
                        label: '铅山县'
                    }, {
                        label: '横峰县'
                    }, {
                        label: '婺源县'
                    }, {
                        label: '上饶县'
                    }, {
                        label: '万年县'
                    }, {
                        label: '弋阳县'
                    }, {
                        label: '余干县'
                    }, {
                        label: '德兴市'
                    }, {
                        label: '东湖区'
                    }],
                    label: '上饶'
                }, {
                    children: [{
                        label: '安源区'
                    }, {
                        label: '上栗县'
                    }, {
                        label: '莲花县'
                    }, {
                        label: '芦溪县'
                    }, {
                        label: '湘东区'
                    }],
                    label: '萍乡'
                }, {
                    children: [{
                        label: '东湖区'
                    }, {
                        label: '南昌县'
                    }, {
                        label: '西湖区'
                    }, {
                        label: '进贤县'
                    }, {
                        label: '新建区'
                    }, {
                        label: '青山湖区'
                    }, {
                        label: '湾里区'
                    }, {
                        label: '青云谱区'
                    }, {
                        label: '安义县'
                    }],
                    label: '南昌'
                }, {
                    children: [{
                        label: '庐山市'
                    }, {
                        label: '浔阳区'
                    }, {
                        label: '修水县'
                    }, {
                        label: '共青城市'
                    }, {
                        label: '九江县'
                    }, {
                        label: '濂溪区'
                    }, {
                        label: '湖口县'
                    }, {
                        label: '瑞昌市'
                    }, {
                        label: '都昌县'
                    }, {
                        label: '德安县'
                    }, {
                        label: '永修县'
                    }, {
                        label: '武宁县'
                    }, {
                        label: '彭泽县'
                    }],
                    label: '九江'
                }, {
                    children: [{
                        label: '珠山区'
                    }, {
                        label: '乐平市'
                    }, {
                        label: '浮梁县'
                    }, {
                        label: '昌江区'
                    }],
                    label: '景德镇'
                }, {
                    children: [{
                        label: '峡江县'
                    }, {
                        label: '泰和县'
                    }, {
                        label: '青原区'
                    }, {
                        label: '吉州区'
                    }, {
                        label: '吉安县'
                    }, {
                        label: '遂川县'
                    }, {
                        label: '井冈山市'
                    }, {
                        label: '永新县'
                    }, {
                        label: '永丰县'
                    }, {
                        label: '新干县'
                    }, {
                        label: '万安县'
                    }, {
                        label: '安福县'
                    }, {
                        label: '吉水县'
                    }],
                    label: '吉安'
                }, {
                    children: [{
                        label: '章贡区'
                    }, {
                        label: '定南县'
                    }, {
                        label: '龙南县'
                    }, {
                        label: '上犹县'
                    }, {
                        label: '寻乌县'
                    }, {
                        label: '兴国县'
                    }, {
                        label: '会昌县'
                    }, {
                        label: '瑞金市'
                    }, {
                        label: '于都县'
                    }, {
                        label: '石城县'
                    }, {
                        label: '南康区'
                    }, {
                        label: '安远县'
                    }, {
                        label: '赣县'
                    }, {
                        label: '大余县'
                    }, {
                        label: '崇义县'
                    }, {
                        label: '全南县'
                    }, {
                        label: '信丰县'
                    }, {
                        label: '宁都县'
                    }],
                    label: '赣州'
                }, {
                    children: [{
                        label: '金溪县'
                    }, {
                        label: '南城县'
                    }, {
                        label: '临川区'
                    }, {
                        label: '东乡县'
                    }, {
                        label: '南丰县'
                    }, {
                        label: '乐安县'
                    }, {
                        label: '广昌县'
                    }, {
                        label: '崇仁县'
                    }, {
                        label: '宜黄县'
                    }, {
                        label: '黎川县'
                    }, {
                        label: '资溪县'
                    }],
                    label: '抚州'
                }]
                },
                {
                    label: '海南',
                children: [{
                    children: [{
                        label: '龙华区'
                    }, {
                        label: '美兰区'
                    }, {
                        label: '琼山区'
                    }],
                    label: '海口'
                }]
                },
                {
                    label: '黑龙江',
                children: [{
                    children: [{
                        label: '南岔区'
                    }, {
                        label: '伊春区'
                    }, {
                        label: '五营区'
                    }, {
                        label: '新青区'
                    }, {
                        label: '美溪区'
                    }, {
                        label: '上甘岭区'
                    }, {
                        label: '翠峦区'
                    }, {
                        label: '友好区'
                    }, {
                        label: '西林区'
                    }, {
                        label: '汤旺河区'
                    }, {
                        label: '铁力市'
                    }, {
                        label: '金山屯区'
                    }, {
                        label: '乌伊岭区'
                    }, {
                        label: '带岭区'
                    }],
                    label: '伊春'
                }, {
                    children: [{
                        label: '北林区'
                    }, {
                        label: '肇东市'
                    }],
                    label: '绥化'
                }, {
                    children: [{
                        label: '宝清县'
                    }, {
                        label: '饶河县'
                    }, {
                        label: '友谊县'
                    }, {
                        label: '集贤县'
                    }, {
                        label: '尖山区'
                    }],
                    label: '双鸭山'
                }, {
                    children: [{
                        label: '龙沙区'
                    }, {
                        label: '克山县'
                    }, {
                        label: '碾子山区'
                    }, {
                        label: '富拉尔基区'
                    }, {
                        label: '拜泉县'
                    }, {
                        label: '富裕县'
                    }, {
                        label: '依安县'
                    }, {
                        label: '龙江县'
                    }, {
                        label: '讷河市'
                    }, {
                        label: '泰来县'
                    }],
                    label: '齐齐哈尔'
                }, {
                    children: [{
                        label: '桃山区'
                    }, {
                        label: '勃利县'
                    }, {
                        label: '新兴区'
                    }],
                    label: '七台河'
                }, {
                    children: [{
                        label: '东宁市'
                    }, {
                        label: '东安区'
                    }, {
                        label: '林口县'
                    }, {
                        label: '绥芬河市'
                    }, {
                        label: '穆棱市'
                    }, {
                        label: '宁安市'
                    }],
                    label: '牡丹江'
                }, {
                    children: [{
                        label: '向阳区'
                    }, {
                        label: '桦南县'
                    }, {
                        label: '汤原县'
                    }, {
                        label: '同江市'
                    }, {
                        label: '富锦市'
                    }, {
                        label: '抚远市'
                    }, {
                        label: '桦川县'
                    }, {
                        label: '郊区'
                    }],
                    label: '佳木斯'
                }, {
                    children: [{
                        label: '虎林市'
                    }, {
                        label: '密山市'
                    }, {
                        label: '鸡冠区'
                    }, {
                        label: '梨树区'
                    }],
                    label: '鸡西'
                }, {
                    children: [{
                        label: '逊克县'
                    }, {
                        label: '北安市'
                    }, {
                        label: '嫩江县'
                    }, {
                        label: '孙吴县'
                    }, {
                        label: '五大连池市'
                    }, {
                        label: '爱辉区'
                    }],
                    label: '黑河'
                }, {
                    children: [{
                        label: '五常市'
                    }, {
                        label: '通河县'
                    }, {
                        label: '巴彦县'
                    }, {
                        label: '延寿县'
                    }, {
                        label: '依兰县'
                    }, {
                        label: '宾县'
                    }, {
                        label: '南岗区'
                    }, {
                        label: '香坊区'
                    }, {
                        label: '道外区'
                    }],
                    label: '哈尔滨'
                }, {
                    children: [{
                        label: '呼玛县'
                    }],
                    label: '大兴安岭'
                }, {
                    children: [{
                        label: '大同区'
                    }, {
                        label: '杜尔伯特蒙古族自治县'
                    }, {
                        label: '肇州县'
                    }],
                    label: '大庆'
                }]
                },
                {
                    label: '天津',
                children: [{
                    children: [{
                        label: '和平区'
                    }, {
                        label: '河北区'
                    }, {
                        label: '宁河区'
                    }, {
                        label: '武清区'
                    }, {
                        label: '滨海新区'
                    }, {
                        label: '南开区'
                    }, {
                        label: '河东区'
                    }, {
                        label: '宝坻区'
                    }, {
                        label: '蓟州区'
                    }, {
                        label: '西青区'
                    }, {
                        label: '红桥区'
                    }, {
                        label: '静海区'
                    }, {
                        label: '北辰区'
                    }, {
                        label: '东丽区'
                    }, {
                        label: '津南区'
                    }],
                    label: '天津'
                }]
                },
                {
                    label: '陕西',
                children: [{
                    children: [{
                        label: '靖边县'
                    }, {
                        label: '榆阳区'
                    }, {
                        label: '米脂县'
                    }, {
                        label: '吴堡县'
                    }, {
                        label: '横山区'
                    }, {
                        label: '佳县'
                    }, {
                        label: '绥德县'
                    }, {
                        label: '定边县'
                    }, {
                        label: '子洲县'
                    }, {
                        label: '神木县'
                    }, {
                        label: '府谷县'
                    }, {
                        label: '清涧县'
                    }],
                    label: '榆林'
                }, {
                    children: [{
                        label: '黄陵县'
                    }, {
                        label: '宝塔区'
                    }, {
                        label: '延川县'
                    }, {
                        label: '志丹县'
                    }, {
                        label: '洛川县'
                    }, {
                        label: '宜川县'
                    }, {
                        label: '延长县'
                    }, {
                        label: '黄龙县'
                    }, {
                        label: '甘泉县'
                    }, {
                        label: '富县'
                    }, {
                        label: '吴起县'
                    }, {
                        label: '子长县'
                    }, {
                        label: '安塞区'
                    }],
                    label: '延安'
                }, {
                    children: [{
                        label: '乾县'
                    }, {
                        label: '秦都区'
                    }, {
                        label: '泾阳县'
                    }, {
                        label: '渭城区'
                    }, {
                        label: '杨陵区'
                    }, {
                        label: '彬县'
                    }, {
                        label: '旬邑县'
                    }, {
                        label: '三原县'
                    }, {
                        label: '永寿县'
                    }, {
                        label: '兴平市'
                    }, {
                        label: '武功县'
                    }, {
                        label: '礼泉县'
                    }, {
                        label: '淳化县'
                    }, {
                        label: '长武县'
                    }],
                    label: '咸阳'
                }, {
                    children: [{
                        label: '新城区'
                    }, {
                        label: '碑林区'
                    }, {
                        label: '户县'
                    }, {
                        label: '莲湖区'
                    }, {
                        label: '长安区'
                    }, {
                        label: '未央区'
                    }, {
                        label: '雁塔区'
                    }, {
                        label: '临潼区'
                    }, {
                        label: '蓝田县'
                    }, {
                        label: '高陵区'
                    }, {
                        label: '阎良区'
                    }, {
                        label: '周至县'
                    }, {
                        label: '灞桥区'
                    }],
                    label: '西安'
                }, {
                    children: [{
                        label: '澄城县'
                    }, {
                        label: '临渭区'
                    }, {
                        label: '韩城市'
                    }, {
                        label: '富平县'
                    }, {
                        label: '大荔县'
                    }, {
                        label: '白水县'
                    }, {
                        label: '华州区'
                    }, {
                        label: '华阴市'
                    }, {
                        label: '合阳县'
                    }, {
                        label: '潼关县'
                    }, {
                        label: '蒲城县'
                    }],
                    label: '渭南'
                }, {
                    children: [{
                        label: '耀州区'
                    }, {
                        label: '宜君县'
                    }, {
                        label: '王益区'
                    }, {
                        label: '印台区'
                    }],
                    label: '铜川'
                }, {
                    children: [{
                        label: '商州区'
                    }, {
                        label: '商南县'
                    }, {
                        label: '柞水县'
                    }, {
                        label: '山阳县'
                    }, {
                        label: '镇安县'
                    }, {
                        label: '洛南县'
                    }, {
                        label: '丹凤县'
                    }],
                    label: '商洛'
                }, {
                    children: [{
                        label: '汉台区'
                    }, {
                        label: '南郑县'
                    }, {
                        label: '勉县'
                    }, {
                        label: '宁强县'
                    }, {
                        label: '佛坪县'
                    }, {
                        label: '留坝县'
                    }, {
                        label: '西乡县'
                    }, {
                        label: '洋县'
                    }, {
                        label: '镇巴县'
                    }, {
                        label: '略阳县'
                    }, {
                        label: '城固县'
                    }],
                    label: '汉中'
                }, {
                    children: [{
                        label: '金台区'
                    }, {
                        label: '渭滨区'
                    }, {
                        label: '凤县'
                    }, {
                        label: '太白县'
                    }, {
                        label: '凤翔县'
                    }, {
                        label: '扶风县'
                    }, {
                        label: '陈仓区'
                    }, {
                        label: '眉县'
                    }, {
                        label: '岐山县'
                    }, {
                        label: '陇县'
                    }, {
                        label: '麟游县'
                    }, {
                        label: '千阳县'
                    }],
                    label: '宝鸡'
                }, {
                    children: [{
                        label: '汉滨区'
                    }, {
                        label: '石泉县'
                    }, {
                        label: '汉阴县'
                    }, {
                        label: '岚皋县'
                    }, {
                        label: '白河县'
                    }, {
                        label: '旬阳县'
                    }, {
                        label: '紫阳县'
                    }, {
                        label: '镇坪县'
                    }, {
                        label: '平利县'
                    }, {
                        label: '宁陕县'
                    }],
                    label: '安康'
                }]
                },
                {
                    label: '贵州',
                children: [{
                    children: [{
                        label: '绥阳县'
                    }, {
                        label: '桐梓县'
                    }, {
                        label: '汇川区'
                    }, {
                        label: '湄潭县'
                    }, {
                        label: '播州区'
                    }, {
                        label: '红花岗区'
                    }, {
                        label: '道真仡佬族苗族自治县'
                    }, {
                        label: '凤冈县'
                    }, {
                        label: '余庆县'
                    }, {
                        label: '仁怀市'
                    }, {
                        label: '习水县'
                    }, {
                        label: '务川仡佬族苗族自治县'
                    }, {
                        label: '正安县'
                    }, {
                        label: '赤水市'
                    }],
                    label: '遵义'
                }, {
                    children: [{
                        label: '兴义市'
                    }, {
                        label: '望谟县'
                    }, {
                        label: '普安县'
                    }, {
                        label: '册亨县'
                    }, {
                        label: '晴隆县'
                    }, {
                        label: '贞丰县'
                    }, {
                        label: '安龙县'
                    }, {
                        label: '兴仁县'
                    }],
                    label: '兴义'
                }, {
                    children: [{
                        label: '松桃苗族自治县'
                    }, {
                        label: '沿河土家族自治县'
                    }, {
                        label: '石阡县'
                    }, {
                        label: '德江县'
                    }, {
                        label: '碧江区'
                    }, {
                        label: '江口县'
                    }, {
                        label: '印江土家族苗族自治县'
                    }, {
                        label: '思南县'
                    }, {
                        label: '万山区'
                    }, {
                        label: '玉屏侗族自治县'
                    }],
                    label: '铜仁'
                }, {
                    children: [{
                        label: '六枝特区'
                    }, {
                        label: '水城县'
                    }, {
                        label: '钟山区'
                    }, {
                        label: '盘县'
                    }, {
                        label: '云岩区'
                    }],
                    label: '六盘水'
                }, {
                    children: [{
                        label: '黎平县'
                    }, {
                        label: '雷山县'
                    }, {
                        label: '锦屏县'
                    }, {
                        label: '凯里市'
                    }, {
                        label: '麻江县'
                    }, {
                        label: '三穗县'
                    }, {
                        label: '天柱县'
                    }, {
                        label: '榕江县'
                    }, {
                        label: '剑河县'
                    }, {
                        label: '黄平县'
                    }, {
                        label: '丹寨县'
                    }, {
                        label: '镇远县'
                    }, {
                        label: '岑巩县'
                    }, {
                        label: '台江县'
                    }, {
                        label: '施秉县'
                    }, {
                        label: '从江县'
                    }],
                    label: '凯里'
                }, {
                    children: [{
                        label: '观山湖区'
                    }, {
                        label: '云岩区'
                    }, {
                        label: '白云区'
                    }, {
                        label: '南明区'
                    }, {
                        label: '修文县'
                    }, {
                        label: '息烽县'
                    }, {
                        label: '清镇市'
                    }, {
                        label: '开阳县'
                    }, {
                        label: '花溪区'
                    }, {
                        label: '乌当区'
                    }],
                    label: '贵阳'
                }, {
                    children: [{
                        label: '都匀市'
                    }, {
                        label: '独山县'
                    }, {
                        label: '三都水族自治县'
                    }, {
                        label: '荔波县'
                    }, {
                        label: '瓮安县'
                    }, {
                        label: '龙里县'
                    }, {
                        label: '平塘县'
                    }, {
                        label: '罗甸县'
                    }, {
                        label: '惠水县'
                    }, {
                        label: '福泉市'
                    }, {
                        label: '长顺县'
                    }, {
                        label: '贵定县'
                    }],
                    label: '都匀'
                }, {
                    children: [{
                        label: '赫章县'
                    }, {
                        label: '大方县'
                    }, {
                        label: '威宁彝族回族苗族自治县'
                    }, {
                        label: '织金县'
                    }, {
                        label: '纳雍县'
                    }, {
                        label: '七星关区'
                    }, {
                        label: '黔西县'
                    }, {
                        label: '金沙县'
                    }],
                    label: '毕节'
                }, {
                    children: [{
                        label: '平坝区'
                    }, {
                        label: '关岭布依族苗族自治县'
                    }, {
                        label: '西秀区'
                    }, {
                        label: '镇宁布依族苗族自治县'
                    }, {
                        label: '普定县'
                    }, {
                        label: '紫云苗族布依族自治县'
                    }],
                    label: '安顺'
                }]
                },
                {
                    label: '新疆',
                children: [{
                    children: [{
                        label: '尼勒克县'
                    }, {
                        label: '昭苏县'
                    }, {
                        label: '巩留县'
                    }, {
                        label: '伊宁市'
                    }, {
                        label: '奎屯市'
                    }, {
                        label: '新源县'
                    }, {
                        label: '伊宁县'
                    }, {
                        label: '察布查尔锡伯自治县'
                    }, {
                        label: '霍城县'
                    }, {
                        label: '霍尔果斯市'
                    }],
                    label: '伊犁'
                }, {
                    children: [{
                        label: '五家渠市'
                    }],
                    label: '五家渠'
                }, {
                    children: [{
                        label: '沙依巴克区'
                    }, {
                        label: '水磨沟区'
                    }, {
                        label: '天山区'
                    }, {
                        label: '新市区'
                    }, {
                        label: '米东区'
                    }, {
                        label: '乌鲁木齐县'
                    }, {
                        label: '达坂城区'
                    }, {
                        label: '头屯河区'
                    }],
                    label: '乌鲁木齐'
                }, {
                    children: [{
                        label: '高昌区'
                    }, {
                        label: '鄯善县'
                    }, {
                        label: '托克逊县'
                    }],
                    label: '吐鲁番'
                }, {
                    children: [{
                        label: '图木舒克市'
                    }],
                    label: '图木舒克'
                }, {
                    children: [{
                        label: '乌苏市'
                    }, {
                        label: '额敏县'
                    }, {
                        label: '托里县'
                    }, {
                        label: '沙湾县'
                    }, {
                        label: '塔城市'
                    }, {
                        label: '裕民县'
                    }, {
                        label: '和布克赛尔蒙古自治县'
                    }],
                    label: '塔城'
                }, {
                    children: [{
                        label: '石河子市'
                    }],
                    label: '石河子'
                }, {
                    children: [{
                        label: '奎屯市'
                    }],
                    label: '奎屯'
                }, {
                    children: [{
                        label: '库尔勒市'
                    }, {
                        label: '尉犁县'
                    }, {
                        label: '焉耆回族自治县'
                    }, {
                        label: '和静县'
                    }, {
                        label: '轮台县'
                    }, {
                        label: '且末县'
                    }, {
                        label: '博湖县'
                    }, {
                        label: '和硕县'
                    }, {
                        label: '若羌县'
                    }],
                    label: '库尔勒'
                }, {
                    children: [{
                        label: '阿图什市'
                    }, {
                        label: '阿合奇县'
                    }, {
                        label: '乌恰县'
                    }, {
                        label: '阿克陶县'
                    }],
                    label: '克州'
                }, {
                    children: [{
                        label: '乌尔禾区'
                    }, {
                        label: '克拉玛依区'
                    }, {
                        label: '白碱滩区'
                    }, {
                        label: '独山子区'
                    }],
                    label: '克拉玛依'
                }, {
                    children: [{
                        label: '喀什市'
                    }, {
                        label: '叶城县'
                    }, {
                        label: '英吉沙县'
                    }, {
                        label: '疏勒县'
                    }, {
                        label: '麦盖提县'
                    }, {
                        label: '莎车县'
                    }, {
                        label: '岳普湖县'
                    }, {
                        label: '巴楚县'
                    }, {
                        label: '疏附县'
                    }, {
                        label: '泽普县'
                    }, {
                        label: '伽师县'
                    }, {
                        label: '塔什库尔干塔吉克自治县'
                    }],
                    label: '喀什'
                }, {
                    children: [{
                        label: '和田市'
                    }, {
                        label: '民丰县'
                    }, {
                        label: '皮山县'
                    }, {
                        label: '墨玉县'
                    }, {
                        label: '策勒县'
                    }, {
                        label: '于田县'
                    }, {
                        label: '洛浦县'
                    }, {
                        label: '和田县'
                    }],
                    label: '和田'
                }, {
                    children: [{
                        label: '伊州区'
                    }, {
                        label: '伊吾县'
                    }, {
                        label: '巴里坤哈萨克自治县'
                    }],
                    label: '哈密'
                }, {
                    children: [{
                        label: '奇台县'
                    }, {
                        label: '昌吉市'
                    }, {
                        label: '玛纳斯县'
                    }, {
                        label: '呼图壁县'
                    }, {
                        label: '阜康市'
                    }, {
                        label: '吉木萨尔县'
                    }, {
                        label: '木垒哈萨克自治县'
                    }],
                    label: '昌吉'
                }, {
                    children: [{
                        label: '精河县'
                    }, {
                        label: '博乐市'
                    }, {
                        label: '温泉县'
                    }, {
                        label: '阿拉山口市'
                    }],
                    label: '博乐'
                }, {
                    children: [{
                        label: '吉木乃县'
                    }, {
                        label: '福海县'
                    }, {
                        label: '富蕴县'
                    }, {
                        label: '阿勒泰市'
                    }, {
                        label: '青河县'
                    }, {
                        label: '哈巴河县'
                    }, {
                        label: '布尔津县'
                    }],
                    label: '阿勒泰'
                }, {
                    children: [{
                        label: '阿拉尔市'
                    }],
                    label: '阿拉尔'
                }, {
                    children: [{
                        label: '阿克苏市'
                    }, {
                        label: '温宿县'
                    }, {
                        label: '沙雅县'
                    }, {
                        label: '乌什县'
                    }, {
                        label: '库车县'
                    }, {
                        label: '阿瓦提县'
                    }, {
                        label: '拜城县'
                    }, {
                        label: '新和县'
                    }],
                    label: '阿克苏'
                }]
                },
                {
                    label: '江苏',
                children: [{
                    children: [{
                        label: '京口区'
                    }, {
                        label: '润州区'
                    }, {
                        label: '句容市'
                    }, {
                        label: '丹徒区'
                    }, {
                        label: '丹阳市'
                    }, {
                        label: '扬中市'
                    }],
                    label: '镇江'
                }, {
                    children: [{
                        label: '邗江区'
                    }, {
                        label: '广陵区'
                    }, {
                        label: '仪征市'
                    }, {
                        label: '高邮市'
                    }, {
                        label: '宝应县'
                    }, {
                        label: '江都区'
                    }],
                    label: '扬州'
                }, {
                    children: [{
                        label: '亭湖区'
                    }, {
                        label: '响水县'
                    }, {
                        label: '盐都区'
                    }, {
                        label: '滨海县'
                    }, {
                        label: '鼓楼区'
                    }, {
                        label: '阜宁县'
                    }, {
                        label: '射阳县'
                    }, {
                        label: '东台市'
                    }, {
                        label: '大丰区'
                    }, {
                        label: '建湖县'
                    }],
                    label: '盐城'
                }, {
                    children: [{
                        label: '鼓楼区'
                    }, {
                        label: '铜山区'
                    }, {
                        label: '泉山区'
                    }, {
                        label: '云龙区'
                    }, {
                        label: '新沂市'
                    }, {
                        label: '邳州市'
                    }, {
                        label: '贾汪区'
                    }, {
                        label: '睢宁县'
                    }, {
                        label: '沛县'
                    }, {
                        label: '丰县'
                    }],
                    label: '徐州'
                }, {
                    children: [{
                        label: '宿城区'
                    }, {
                        label: '沭阳县'
                    }, {
                        label: '宿豫区'
                    }, {
                        label: '泗洪县'
                    }, {
                        label: '泗阳县'
                    }],
                    label: '宿迁'
                }, {
                    children: [{
                        label: '宜兴市'
                    }, {
                        label: '惠山区'
                    }, {
                        label: '江阴市'
                    }, {
                        label: '锡山区'
                    }, {
                        label: '梁溪区'
                    }, {
                        label: '滨湖区'
                    }, {
                        label: '新吴区'
                    }],
                    label: '无锡'
                }, {
                    children: [{
                        label: '高港区'
                    }, {
                        label: '海陵区'
                    }, {
                        label: '姜堰区'
                    }, {
                        label: '泰兴市'
                    }, {
                        label: '兴化市'
                    }, {
                        label: '靖江市'
                    }, {
                        label: '姑苏区'
                    }],
                    label: '泰州'
                }, {
                    children: [{
                        label: '昆山市'
                    }, {
                        label: '吴中区'
                    }, {
                        label: '姑苏区'
                    }, {
                        label: '张家港市'
                    }, {
                        label: '虎丘区'
                    }, {
                        label: '相城区'
                    }, {
                        label: '太仓市'
                    }, {
                        label: '吴江区'
                    }],
                    label: '苏州'
                }, {
                    children: [{
                        label: '港闸区'
                    }, {
                        label: '崇川区'
                    }, {
                        label: '海安县'
                    }, {
                        label: '通州区'
                    }, {
                        label: '海门市'
                    }, {
                        label: '如东县'
                    }, {
                        label: '启东市'
                    }, {
                        label: '如皋市'
                    }],
                    label: '南通'
                }, {
                    children: [{
                        label: '鼓楼区'
                    }, {
                        label: '雨花台区'
                    }, {
                        label: '栖霞区'
                    }, {
                        label: '玄武区'
                    }, {
                        label: '江宁区'
                    }, {
                        label: '秦淮区'
                    }, {
                        label: '浦口区'
                    }, {
                        label: '六合区'
                    }, {
                        label: '高淳区'
                    }, {
                        label: '建邺区'
                    }, {
                        label: '溧水区'
                    }],
                    label: '南京'
                }, {
                    children: [{
                        label: '灌南县'
                    }, {
                        label: '海州区'
                    }, {
                        label: '赣榆区'
                    }, {
                        label: '连云区'
                    }, {
                        label: '灌云县'
                    }, {
                        label: '东海县'
                    }],
                    label: '连云港'
                }, {
                    children: [{
                        label: '淮阴区'
                    }, {
                        label: '清江浦区'
                    }, {
                        label: '淮安区'
                    }, {
                        label: '涟水县'
                    }, {
                        label: '洪泽区'
                    }, {
                        label: '金湖县'
                    }, {
                        label: '盱眙县'
                    }],
                    label: '淮安'
                }, {
                    children: [{
                        label: '武进区'
                    }, {
                        label: '新北区'
                    }, {
                        label: '钟楼区'
                    }, {
                        label: '天宁区'
                    }, {
                        label: '金坛区'
                    }, {
                        label: '溧阳市'
                    }],
                    label: '常州'
                }, {
                    children: [{
                        label: '常熟市'
                    }],
                    label: '常熟'
                }]
                },
                {
                    label: '安徽',
                children: [{
                    children: [{
                        label: '旌德县'
                    }, {
                        label: '宁国市'
                    }, {
                        label: '广德县'
                    }, {
                        label: '郎溪县'
                    }, {
                        label: '宣州区'
                    }, {
                        label: '泾县'
                    }, {
                        label: '绩溪县'
                    }, {
                        label: '蜀山区'
                    }],
                    label: '宣城'
                }, {
                    children: [{
                        label: '埇桥区'
                    }, {
                        label: '砀山县'
                    }, {
                        label: '萧县'
                    }, {
                        label: '灵璧县'
                    }, {
                        label: '泗县'
                    }],
                    label: '宿州'
                }, {
                    children: [{
                        label: '镜湖区'
                    }, {
                        label: '弋江区'
                    }, {
                        label: '鸠江区'
                    }, {
                        label: '三山区'
                    }, {
                        label: '无为县'
                    }, {
                        label: '芜湖县'
                    }, {
                        label: '繁昌县'
                    }, {
                        label: '南陵县'
                    }],
                    label: '芜湖'
                }, {
                    children: [{
                        label: '铜官区'
                    }, {
                        label: '义安区'
                    }, {
                        label: '枞阳县'
                    }, {
                        label: '郊区'
                    }],
                    label: '铜陵'
                }, {
                    children: [{
                        label: '花山区'
                    }, {
                        label: '雨山区'
                    }, {
                        label: '和县'
                    }, {
                        label: '当涂县'
                    }, {
                        label: '博望区'
                    }, {
                        label: '含山县'
                    }],
                    label: '马鞍山'
                }, {
                    children: [{
                        label: '金安区'
                    }, {
                        label: '裕安区'
                    }, {
                        label: '霍山县'
                    }, {
                        label: '金寨县'
                    }, {
                        label: '舒城县'
                    }, {
                        label: '霍邱县'
                    }, {
                        label: '叶集区'
                    }],
                    label: '六安'
                }, {
                    children: [{
                        label: '屯溪区'
                    }, {
                        label: '黄山区'
                    }, {
                        label: '休宁县'
                    }, {
                        label: '黟县'
                    }, {
                        label: '徽州区'
                    }, {
                        label: '歙县'
                    }, {
                        label: '祁门县'
                    }],
                    label: '黄山'
                }, {
                    children: [{
                        label: '谢家集区'
                    }, {
                        label: '田家庵区'
                    }, {
                        label: '大通区'
                    }, {
                        label: '凤台县'
                    }, {
                        label: '寿县'
                    }, {
                        label: '八公山区'
                    }, {
                        label: '潘集区'
                    }],
                    label: '淮南'
                }, {
                    children: [{
                        label: '相山区'
                    }, {
                        label: '烈山区'
                    }, {
                        label: '濉溪县'
                    }, {
                        label: '杜集区'
                    }],
                    label: '淮北'
                }, {
                    children: [{
                        label: '巢湖区'
                    }, {
                        label: '瑶海区'
                    }, {
                        label: '蜀山区'
                    }, {
                        label: '包河区'
                    }, {
                        label: '庐阳区'
                    }, {
                        label: '肥西县'
                    }, {
                        label: '肥东县'
                    }, {
                        label: '长丰县'
                    }, {
                        label: '庐江县'
                    }],
                    label: '合肥'
                }, {
                    children: [{
                        label: '临泉县'
                    }, {
                        label: '颍州区'
                    }, {
                        label: '颍东区'
                    }, {
                        label: '颍上县'
                    }, {
                        label: '界首市'
                    }, {
                        label: '颍泉区'
                    }, {
                        label: '阜南县'
                    }, {
                        label: '太和县'
                    }, {
                        label: '蜀山区'
                    }],
                    label: '阜阳'
                }, {
                    children: [{
                        label: '琅琊区'
                    }, {
                        label: '来安县'
                    }, {
                        label: '凤阳县'
                    }, {
                        label: '南谯区'
                    }, {
                        label: '天长市'
                    }, {
                        label: '明光市'
                    }, {
                        label: '全椒县'
                    }, {
                        label: '定远县'
                    }],
                    label: '滁州'
                }, {
                    children: [{
                        label: '贵池区'
                    }, {
                        label: '东至县'
                    }, {
                        label: '石台县'
                    }, {
                        label: '青阳县'
                    }],
                    label: '池州'
                }, {
                    children: [{
                        label: '巢湖市'
                    }],
                    label: '巢湖'
                }, {
                    children: [{
                        label: '谯城区'
                    }, {
                        label: '涡阳县'
                    }, {
                        label: '利辛县'
                    }, {
                        label: '蒙城县'
                    }],
                    label: '亳州'
                }, {
                    children: [{
                        label: '蚌山区'
                    }, {
                        label: '禹会区'
                    }, {
                        label: '龙子湖区'
                    }, {
                        label: '五河县'
                    }, {
                        label: '怀远县'
                    }, {
                        label: '淮上区'
                    }, {
                        label: '固镇县'
                    }],
                    label: '蚌埠'
                }, {
                    children: [{
                        label: '迎江区'
                    }, {
                        label: '宿松县'
                    }, {
                        label: '大观区'
                    }, {
                        label: '宜秀区'
                    }, {
                        label: '太湖县'
                    }, {
                        label: '望江县'
                    }, {
                        label: '岳西县'
                    }, {
                        label: '桐城市'
                    }, {
                        label: '怀宁县'
                    }, {
                        label: '潜山县'
                    }],
                    label: '安庆'
                }]
                },
                {
                    label: '西藏',
                children: [{
                    children: [{
                        label: '城关区'
                    }, {
                        label: '堆龙德庆区'
                    }],
                    label: '拉萨'
                }]
                },
                {
                    label: '上海',
                children: [{
                    children: [{
                        label: '普陀区'
                    }, {
                        label: '宝山区'
                    }, {
                        label: '徐汇区'
                    }, {
                        label: '长宁区'
                    }, {
                        label: '黄浦区'
                    }, {
                        label: '东阳市'
                    }, {
                        label: '崇明区'
                    }, {
                        label: '浦东新区'
                    }],
                    label: '上海'
                }]
                },
                {
                    label: '吉林',
                children: [{
                    children: [{
                        label: '朝阳区'
                    }, {
                        label: '二道区'
                    }, {
                        label: '德惠市'
                    }, {
                        label: '九台区'
                    }, {
                        label: '南关区'
                    }, {
                        label: '宽城区'
                    }, {
                        label: '绿园区'
                    }, {
                        label: '双阳区'
                    }, {
                        label: '农安县'
                    }, {
                        label: '榆树市'
                    }],
                    label: '长春'
                }, {
                    children: [{
                        label: '延吉市'
                    }],
                    label: '延吉'
                }, {
                    children: [{
                        label: '东昌区'
                    }, {
                        label: '集安市'
                    }, {
                        label: '辉南县'
                    }],
                    label: '通化'
                }, {
                    children: [{
                        label: '扶余市'
                    }, {
                        label: '宁江区'
                    }, {
                        label: '长岭县'
                    }],
                    label: '松原'
                }, {
                    children: [{
                        label: '双辽市'
                    }, {
                        label: '铁西区'
                    }, {
                        label: '公主岭市'
                    }, {
                        label: '梨树县'
                    }, {
                        label: '伊通满族自治县'
                    }, {
                        label: '铁东区'
                    }],
                    label: '四平'
                }, {
                    children: [{
                        label: '梅河口市'
                    }],
                    label: '梅河口'
                }, {
                    children: [{
                        label: '东丰县'
                    }, {
                        label: '东辽县'
                    }, {
                        label: '龙山区'
                    }],
                    label: '辽源'
                }, {
                    children: [{
                        label: '昌邑区'
                    }, {
                        label: '桦甸市'
                    }, {
                        label: '船营区'
                    }, {
                        label: '磐石市'
                    }, {
                        label: '丰满区'
                    }, {
                        label: '龙潭区'
                    }, {
                        label: '舒兰市'
                    }, {
                        label: '蛟河市'
                    }],
                    label: '吉林'
                }, {
                    children: [{
                        label: '珲春市'
                    }],
                    label: '珲春'
                }, {
                    children: [{
                        label: '浑江区'
                    }, {
                        label: '靖宇县'
                    }, {
                        label: '长白朝鲜族自治县'
                    }, {
                        label: '临江市'
                    }, {
                        label: '江源区'
                    }, {
                        label: '抚松县'
                    }],
                    label: '白山'
                }, {
                    children: [{
                        label: '通榆县'
                    }, {
                        label: '大安市'
                    }, {
                        label: '洮北区'
                    }, {
                        label: '镇赉县'
                    }],
                    label: '白城'
                }]
                },
                {
                    label: '山西',
                children: [{
                    children: [{
                        label: '屯留县'
                    }, {
                        label: '长子县'
                    }, {
                        label: '壶关县'
                    }, {
                        label: '城区'
                    }, {
                        label: '潞城市'
                    }, {
                        label: '长治县'
                    }, {
                        label: '郊区'
                    }, {
                        label: '武乡县'
                    }, {
                        label: '黎城县'
                    }, {
                        label: '平顺县'
                    }, {
                        label: '沁源县'
                    }, {
                        label: '襄垣县'
                    }, {
                        label: '沁县'
                    }],
                    label: '长治'
                }, {
                    children: [{
                        label: '新绛县'
                    }, {
                        label: '夏县'
                    }, {
                        label: '盐湖区'
                    }, {
                        label: '永济市'
                    }, {
                        label: '稷山县'
                    }, {
                        label: '垣曲县'
                    }, {
                        label: '绛县'
                    }, {
                        label: '河津市'
                    }, {
                        label: '万荣县'
                    }, {
                        label: '平陆县'
                    }, {
                        label: '临猗县'
                    }, {
                        label: '闻喜县'
                    }, {
                        label: '芮城县'
                    }],
                    label: '运城'
                }, {
                    children: [{
                        label: '平定县'
                    }, {
                        label: '城区'
                    }, {
                        label: '矿区'
                    }, {
                        label: '盂县'
                    }, {
                        label: '郊区'
                    }],
                    label: '阳泉'
                }, {
                    children: [{
                        label: '河曲县'
                    }, {
                        label: '保德县'
                    }, {
                        label: '偏关县'
                    }, {
                        label: '五台县'
                    }, {
                        label: '代县'
                    }, {
                        label: '五寨县'
                    }, {
                        label: '宁武县'
                    }, {
                        label: '原平市'
                    }, {
                        label: '繁峙县'
                    }, {
                        label: '忻府区'
                    }, {
                        label: '定襄县'
                    }, {
                        label: '神池县'
                    }, {
                        label: '静乐县'
                    }, {
                        label: '岢岚县'
                    }],
                    label: '忻州'
                }, {
                    children: [{
                        label: '小店区'
                    }, {
                        label: '迎泽区'
                    }, {
                        label: '万柏林区'
                    }, {
                        label: '晋源区'
                    }, {
                        label: '古交市'
                    }, {
                        label: '杏花岭区'
                    }, {
                        label: '娄烦县'
                    }, {
                        label: '尖草坪区'
                    }, {
                        label: '清徐县'
                    }, {
                        label: '西工区'
                    }, {
                        label: '阳曲县'
                    }],
                    label: '太原'
                }, {
                    children: [{
                        label: '怀仁县'
                    }, {
                        label: '山阴县'
                    }, {
                        label: '朔城区'
                    }, {
                        label: '应县'
                    }, {
                        label: '右玉县'
                    }, {
                        label: '平鲁区'
                    }],
                    label: '朔州'
                }, {
                    children: [{
                        label: '文水县'
                    }, {
                        label: '孝义市'
                    }, {
                        label: '交城县'
                    }, {
                        label: '汾阳市'
                    }, {
                        label: '中阳县'
                    }, {
                        label: '离石区'
                    }, {
                        label: '方山县'
                    }, {
                        label: '交口县'
                    }, {
                        label: '兴县'
                    }, {
                        label: '柳林县'
                    }, {
                        label: '岚县'
                    }, {
                        label: '临县'
                    }, {
                        label: '石楼县'
                    }],
                    label: '吕梁'
                }, {
                    children: [{
                        label: '侯马市'
                    }, {
                        label: '尧都区'
                    }, {
                        label: '汾西县'
                    }, {
                        label: '洪洞县'
                    }, {
                        label: '襄汾县'
                    }, {
                        label: '古县'
                    }, {
                        label: '霍州市'
                    }, {
                        label: '乡宁县'
                    }, {
                        label: '曲沃县'
                    }, {
                        label: '隰县'
                    }, {
                        label: '安泽县'
                    }, {
                        label: '翼城县'
                    }, {
                        label: '永和县'
                    }, {
                        label: '浮山县'
                    }, {
                        label: '蒲县'
                    }, {
                        label: '吉县'
                    }, {
                        label: '大宁县'
                    }],
                    label: '临汾'
                }, {
                    children: [{
                        label: '左权县'
                    }, {
                        label: '灵石县'
                    }, {
                        label: '平遥县'
                    }, {
                        label: '和顺县'
                    }, {
                        label: '榆次区'
                    }, {
                        label: '昔阳县'
                    }, {
                        label: '介休市'
                    }, {
                        label: '寿阳县'
                    }, {
                        label: '榆社县'
                    }, {
                        label: '祁县'
                    }, {
                        label: '太谷县'
                    }],
                    label: '晋中'
                }, {
                    children: [{
                        label: '陵川县'
                    }, {
                        label: '沁水县'
                    }, {
                        label: '城区'
                    }, {
                        label: '泽州县'
                    }, {
                        label: '阳城县'
                    }, {
                        label: '高平市'
                    }],
                    label: '晋城'
                }, {
                    children: [{
                        label: '广灵县'
                    }, {
                        label: '城区'
                    }, {
                        label: '阳高县'
                    }, {
                        label: '灵丘县'
                    }, {
                        label: '南郊区'
                    }, {
                        label: '浑源县'
                    }, {
                        label: '矿区'
                    }, {
                        label: '天镇县'
                    }, {
                        label: '新荣区'
                    }, {
                        label: '左云县'
                    }, {
                        label: '大同县'
                    }],
                    label: '大同'
                }]
                },
                {
                    label: '宁夏',
                children: [{
                    children: [{
                        label: '沙坡头区'
                    }, {
                        label: '中宁县'
                    }, {
                        label: '海原县'
                    }],
                    label: '中卫'
                }, {
                    children: [{
                        label: '金凤区'
                    }, {
                        label: '兴庆区'
                    }, {
                        label: '永宁县'
                    }, {
                        label: '西夏区'
                    }, {
                        label: '灵武市'
                    }, {
                        label: '贺兰县'
                    }, {
                        label: '东湖区'
                    }],
                    label: '银川'
                }, {
                    children: [{
                        label: '红寺堡区'
                    }, {
                        label: '利通区'
                    }, {
                        label: '盐池县'
                    }, {
                        label: '青铜峡市'
                    }, {
                        label: '同心县'
                    }],
                    label: '吴忠'
                }, {
                    children: [{
                        label: '平罗县'
                    }, {
                        label: '大武口区'
                    }, {
                        label: '惠农区'
                    }],
                    label: '石嘴山'
                }, {
                    children: [{
                        label: '原州区'
                    }, {
                        label: '隆德县'
                    }, {
                        label: '西吉县'
                    }, {
                        label: '彭阳县'
                    }, {
                        label: '泾源县'
                    }, {
                        label: '金凤区'
                    }],
                    label: '固原'
                }]
                },
                {
                    label: '甘肃',
                children: [{
                    children: [{
                        label: '甘州区'
                    }, {
                        label: '山丹县'
                    }, {
                        label: '民乐县'
                    }, {
                        label: '高台县'
                    }, {
                        label: '临泽县'
                    }, {
                        label: '肃南裕固族自治县'
                    }],
                    label: '张掖'
                }, {
                    children: [{
                        label: '天祝藏族自治县'
                    }, {
                        label: '凉州区'
                    }, {
                        label: '古浪县'
                    }, {
                        label: '民勤县'
                    }],
                    label: '武威'
                }, {
                    children: [{
                        label: '张家川回族自治县'
                    }, {
                        label: '武山县'
                    }, {
                        label: '清水县'
                    }, {
                        label: '秦州区'
                    }, {
                        label: '甘谷县'
                    }, {
                        label: '麦积区'
                    }, {
                        label: '秦安县'
                    }, {
                        label: '七里河区'
                    }],
                    label: '天水'
                }, {
                    children: [{
                        label: '镇原县'
                    }, {
                        label: '西峰区'
                    }, {
                        label: '宁县'
                    }, {
                        label: '华池县'
                    }, {
                        label: '环县'
                    }, {
                        label: '庆城县'
                    }, {
                        label: '合水县'
                    }, {
                        label: '正宁县'
                    }],
                    label: '庆阳'
                }, {
                    children: [{
                        label: '崆峒区'
                    }, {
                        label: '静宁县'
                    }, {
                        label: '庄浪县'
                    }, {
                        label: '泾川县'
                    }, {
                        label: '华亭县'
                    }, {
                        label: '灵台县'
                    }, {
                        label: '崇信县'
                    }],
                    label: '平凉'
                }, {
                    children: [{
                        label: '两当县'
                    }, {
                        label: '宕昌县'
                    }, {
                        label: '武都区'
                    }, {
                        label: '康县'
                    }, {
                        label: '成县'
                    }, {
                        label: '礼县'
                    }, {
                        label: '西和县'
                    }, {
                        label: '文县'
                    }, {
                        label: '徽县'
                    }],
                    label: '陇南'
                }, {
                    children: [{
                        label: '临夏市'
                    }, {
                        label: '广河县'
                    }, {
                        label: '和政县'
                    }, {
                        label: '永靖县'
                    }, {
                        label: '临夏县'
                    }, {
                        label: '积石山保安族东乡族撒拉族自治县'
                    }, {
                        label: '康乐县'
                    }, {
                        label: '东乡族自治县'
                    }],
                    label: '临夏'
                }, {
                    children: [{
                        label: '城关区'
                    }, {
                        label: '西固区'
                    }, {
                        label: '榆中县'
                    }, {
                        label: '安宁区'
                    }, {
                        label: '七里河区'
                    }, {
                        label: '永登县'
                    }, {
                        label: '红古区'
                    }, {
                        label: '嘉峪关市'
                    }, {
                        label: '皋兰县'
                    }],
                    label: '兰州'
                }, {
                    children: [{
                        label: '酒泉嘉峪关'
                    }],
                    label: '酒泉嘉峪关'
                }, {
                    children: [{
                        label: '肃州区'
                    }, {
                        label: '敦煌市'
                    }, {
                        label: '金塔县'
                    }, {
                        label: '瓜州县'
                    }, {
                        label: '玉门市'
                    }, {
                        label: '肃北蒙古族自治县'
                    }, {
                        label: '阿克塞哈萨克族自治县'
                    }],
                    label: '酒泉'
                }, {
                    children: [{
                        label: '金昌武威'
                    }],
                    label: '金昌武威'
                }, {
                    children: [{
                        label: '金川区'
                    }, {
                        label: '永昌县'
                    }],
                    label: '金昌'
                }, {
                    children: [{
                        label: '嘉峪关市'
                    }],
                    label: '嘉峪关'
                }, {
                    children: [{
                        label: '夏河县'
                    }, {
                        label: '舟曲县'
                    }, {
                        label: '合作市'
                    }, {
                        label: '临潭县'
                    }, {
                        label: '迭部县'
                    }, {
                        label: '卓尼县'
                    }, {
                        label: '玛曲县'
                    }, {
                        label: '碌曲县'
                    }],
                    label: '甘南'
                }, {
                    children: [{
                        label: '临洮县'
                    }, {
                        label: '安定区'
                    }, {
                        label: '岷县'
                    }, {
                        label: '通渭县'
                    }, {
                        label: '渭源县'
                    }, {
                        label: '陇西县'
                    }, {
                        label: '漳县'
                    }],
                    label: '定西'
                }, {
                    children: [{
                        label: '景泰县'
                    }, {
                        label: '靖远县'
                    }, {
                        label: '白银区'
                    }, {
                        label: '平川区'
                    }, {
                        label: '会宁县'
                    }],
                    label: '白银'
                }]
                },
                {
                    label: '四川',
                children: [{
                    children: [{
                        label: '贡井区'
                    }, {
                        label: '自流井区'
                    }, {
                        label: '荣县'
                    }, {
                        label: '富顺县'
                    }, {
                        label: '大安区'
                    }, {
                        label: '沿滩区'
                    }],
                    label: '自贡'
                }, {
                    children: [{
                        label: '雁江区'
                    }, {
                        label: '安岳县'
                    }, {
                        label: '乐至县'
                    }],
                    label: '资阳'
                }, {
                    children: [{
                        label: '南溪区'
                    }, {
                        label: '翠屏区'
                    }, {
                        label: '长宁县'
                    }, {
                        label: '高县'
                    }, {
                        label: '江安县'
                    }, {
                        label: '兴文县'
                    }, {
                        label: '珙县'
                    }, {
                        label: '宜宾县'
                    }, {
                        label: '屏山县'
                    }, {
                        label: '筠连县'
                    }, {
                        label: '青羊区'
                    }],
                    label: '宜宾'
                }, {
                    children: [{
                        label: '名山区'
                    }, {
                        label: '雨城区'
                    }, {
                        label: '芦山县'
                    }, {
                        label: '宝兴县'
                    }, {
                        label: '荥经县'
                    }, {
                        label: '天全县'
                    }, {
                        label: '汉源县'
                    }, {
                        label: '石棉县'
                    }],
                    label: '雅安'
                }, {
                    children: [{
                        label: '船山区'
                    }, {
                        label: '射洪县'
                    }, {
                        label: '蓬溪县'
                    }, {
                        label: '大英县'
                    }, {
                        label: '安居区'
                    }],
                    label: '遂宁'
                }, {
                    children: [{
                        label: '东区'
                    }, {
                        label: '仁和区'
                    }, {
                        label: '米易县'
                    }, {
                        label: '盐边县'
                    }, {
                        label: '西区'
                    }],
                    label: '攀枝花'
                }, {
                    children: [{
                        label: '市中区'
                    }, {
                        label: '隆昌县'
                    }, {
                        label: '东兴区'
                    }, {
                        label: '威远县'
                    }, {
                        label: '资中县'
                    }, {
                        label: '涪城区'
                    }],
                    label: '内江'
                }, {
                    children: [{
                        label: '顺庆区'
                    }, {
                        label: '西充县'
                    }, {
                        label: '仪陇县'
                    }, {
                        label: '高坪区'
                    }, {
                        label: '阆中市'
                    }, {
                        label: '嘉陵区'
                    }, {
                        label: '蓬安县'
                    }, {
                        label: '南部县'
                    }, {
                        label: '营山县'
                    }, {
                        label: '青羊区'
                    }],
                    label: '南充'
                }, {
                    children: [{
                        label: '游仙区'
                    }, {
                        label: '江油市'
                    }, {
                        label: '涪城区'
                    }, {
                        label: '三台县'
                    }, {
                        label: '安州区'
                    }, {
                        label: '盐亭县'
                    }, {
                        label: '梓潼县'
                    }, {
                        label: '平武县'
                    }, {
                        label: '北川羌族自治县'
                    }],
                    label: '绵阳'
                }, {
                    children: [{
                        label: '东坡区'
                    }, {
                        label: '洪雅县'
                    }, {
                        label: '彭山区'
                    }, {
                        label: '青神县'
                    }, {
                        label: '仁寿县'
                    }, {
                        label: '丹棱县'
                    }],
                    label: '眉山'
                }, {
                    children: [{
                        label: '江阳区'
                    }, {
                        label: '龙马潭区'
                    }, {
                        label: '合江县'
                    }, {
                        label: '纳溪区'
                    }, {
                        label: '古蔺县'
                    }, {
                        label: '泸县'
                    }, {
                        label: '叙永县'
                    }],
                    label: '泸州'
                }, {
                    children: [{
                        label: '西昌市'
                    }, {
                        label: '会理县'
                    }, {
                        label: '盐源县'
                    }, {
                        label: '越西县'
                    }, {
                        label: '昭觉县'
                    }, {
                        label: '美姑县'
                    }, {
                        label: '金阳县'
                    }, {
                        label: '会东县'
                    }, {
                        label: '雷波县'
                    }, {
                        label: '普格县'
                    }, {
                        label: '喜德县'
                    }, {
                        label: '德昌县'
                    }, {
                        label: '冕宁县'
                    }, {
                        label: '甘洛县'
                    }, {
                        label: '宁南县'
                    }, {
                        label: '布拖县'
                    }, {
                        label: '木里藏族自治县'
                    }],
                    label: '凉山'
                }, {
                    children: [{
                        label: '沙湾区'
                    }, {
                        label: '市中区'
                    }, {
                        label: '马边彝族自治县'
                    }, {
                        label: '峨边彝族自治县'
                    }, {
                        label: '峨眉山市'
                    }, {
                        label: '夹江县'
                    }, {
                        label: '犍为县'
                    }, {
                        label: '沐川县'
                    }, {
                        label: '井研县'
                    }, {
                        label: '五通桥区'
                    }, {
                        label: '金口河区'
                    }],
                    label: '乐山'
                }, {
                    children: [{
                        label: '利州区'
                    }, {
                        label: '朝天区'
                    }, {
                        label: '苍溪县'
                    }, {
                        label: '旺苍县'
                    }, {
                        label: '青川县'
                    }, {
                        label: '昭化区'
                    }, {
                        label: '剑阁县'
                    }],
                    label: '广元'
                }, {
                    children: [{
                        label: '前锋区'
                    }, {
                        label: '广安区'
                    }, {
                        label: '岳池县'
                    }, {
                        label: '武胜县'
                    }, {
                        label: '邻水县'
                    }, {
                        label: '华蓥市'
                    }, {
                        label: '青羊区'
                    }],
                    label: '广安'
                }, {
                    children: [{
                        label: '康定市'
                    }, {
                        label: '稻城县'
                    }, {
                        label: '泸定县'
                    }, {
                        label: '丹巴县'
                    }, {
                        label: '得荣县'
                    }, {
                        label: '德格县'
                    }, {
                        label: '乡城县'
                    }, {
                        label: '九龙县'
                    }, {
                        label: '石渠县'
                    }, {
                        label: '雅江县'
                    }, {
                        label: '巴塘县'
                    }, {
                        label: '理塘县'
                    }, {
                        label: '新龙县'
                    }, {
                        label: '道孚县'
                    }, {
                        label: '白玉县'
                    }, {
                        label: '甘孜县'
                    }, {
                        label: '炉霍县'
                    }, {
                        label: '色达县'
                    }],
                    label: '甘孜'
                }, {
                    children: [{
                        label: '旌阳区'
                    }, {
                        label: '罗江县'
                    }, {
                        label: '中江县'
                    }, {
                        label: '广汉市'
                    }, {
                        label: '绵竹市'
                    }, {
                        label: '什邡市'
                    }, {
                        label: '青羊区'
                    }],
                    label: '德阳'
                }, {
                    children: [{
                        label: '达川区'
                    }, {
                        label: '通川区'
                    }, {
                        label: '宣汉县'
                    }, {
                        label: '大竹县'
                    }, {
                        label: '开江县'
                    }, {
                        label: '渠县'
                    }, {
                        label: '万源市'
                    }, {
                        label: '涪城区'
                    }],
                    label: '达州'
                }, {
                    children: [{
                        label: '锦江区'
                    }, {
                        label: '龙泉驿区'
                    }, {
                        label: '新都区'
                    }, {
                        label: '成华区'
                    }, {
                        label: '武侯区'
                    }, {
                        label: '简阳市'
                    }, {
                        label: '郫县'
                    }, {
                        label: '双流区'
                    }, {
                        label: '金牛区'
                    }, {
                        label: '青羊区'
                    }, {
                        label: '大邑县'
                    }, {
                        label: '青白江区'
                    }, {
                        label: '温江区'
                    }, {
                        label: '邛崃市'
                    }, {
                        label: '崇州市'
                    }, {
                        label: '彭州市'
                    }, {
                        label: '蒲江县'
                    }, {
                        label: '都江堰市'
                    }, {
                        label: '金堂县'
                    }, {
                        label: '新津县'
                    }],
                    label: '成都'
                }, {
                    children: [{
                        label: '巴州区'
                    }, {
                        label: '恩阳区'
                    }, {
                        label: '南江县'
                    }, {
                        label: '通江县'
                    }, {
                        label: '平昌县'
                    }],
                    label: '巴中'
                }, {
                    children: [{
                        label: '马尔康市'
                    }, {
                        label: '若尔盖县'
                    }, {
                        label: '汶川县'
                    }, {
                        label: '茂县'
                    }, {
                        label: '阿坝县'
                    }, {
                        label: '黑水县'
                    }, {
                        label: '理县'
                    }, {
                        label: '九寨沟县'
                    }, {
                        label: '金川县'
                    }, {
                        label: '壤塘县'
                    }, {
                        label: '红原县'
                    }, {
                        label: '松潘县'
                    }, {
                        label: '小金县'
                    }],
                    label: '阿坝'
                }]
                },
                {
                    label: '浙江',
                children: [{
                    children: [{
                        label: '嵊泗县'
                    }, {
                        label: '岱山县'
                    }, {
                        label: '普陀区'
                    }, {
                        label: '定海区'
                    }],
                    label: '舟山'
                }, {
                    children: [{
                        label: '龙湾区'
                    }, {
                        label: '瓯海区'
                    }, {
                        label: '永嘉县'
                    }, {
                        label: '泰顺县'
                    }, {
                        label: '鹿城区'
                    }, {
                        label: '平阳县'
                    }, {
                        label: '乐清市'
                    }, {
                        label: '瑞安市'
                    }, {
                        label: '苍南县'
                    }, {
                        label: '洞头区'
                    }, {
                        label: '文成县'
                    }],
                    label: '温州'
                }, {
                    children: [{
                        label: '椒江区'
                    }, {
                        label: '黄岩区'
                    }, {
                        label: '玉环县'
                    }, {
                        label: '路桥区'
                    }, {
                        label: '临海市'
                    }, {
                        label: '天台县'
                    }, {
                        label: '三门县'
                    }, {
                        label: '温岭市'
                    }, {
                        label: '仙居县'
                    }],
                    label: '台州'
                }, {
                    children: [{
                        label: '上虞区'
                    }, {
                        label: '诸暨市'
                    }, {
                        label: '柯桥区'
                    }, {
                        label: '越城区'
                    }, {
                        label: '嵊州市'
                    }, {
                        label: '新昌县'
                    }, {
                        label: '江干区'
                    }],
                    label: '绍兴'
                }, {
                    children: [{
                        label: '衢江区'
                    }, {
                        label: '龙游县'
                    }, {
                        label: '开化县'
                    }, {
                        label: '江山市'
                    }, {
                        label: '柯城区'
                    }, {
                        label: '常山县'
                    }],
                    label: '衢州'
                }, {
                    children: [{
                        label: '镇海区'
                    }, {
                        label: '奉化市'
                    }, {
                        label: '宁海县'
                    }, {
                        label: '慈溪市'
                    }, {
                        label: '余姚市'
                    }, {
                        label: '鄞州区'
                    }, {
                        label: '海曙区'
                    }, {
                        label: '北仑区'
                    }, {
                        label: '象山县'
                    }, {
                        label: '江北区'
                    }, {
                        label: '江东区'
                    }, {
                        label: '萧山区'
                    }],
                    label: '宁波'
                }, {
                    children: [{
                        label: '青田县'
                    }, {
                        label: '缙云县'
                    }, {
                        label: '景宁畲族自治县'
                    }, {
                        label: '莲都区'
                    }, {
                        label: '遂昌县'
                    }, {
                        label: '庆元县'
                    }, {
                        label: '云和县'
                    }, {
                        label: '龙泉市'
                    }, {
                        label: '松阳县'
                    }],
                    label: '丽水'
                }, {
                    children: [{
                        label: '东阳市'
                    }, {
                        label: '婺城区'
                    }, {
                        label: '永康市'
                    }, {
                        label: '义乌市'
                    }, {
                        label: '浦江县'
                    }, {
                        label: '武义县'
                    }, {
                        label: '金东区'
                    }, {
                        label: '兰溪市'
                    }, {
                        label: '磐安县'
                    }],
                    label: '金华'
                }, {
                    children: [{
                        label: '秀洲区'
                    }, {
                        label: '嘉善县'
                    }, {
                        label: '海宁市'
                    }, {
                        label: '桐乡市'
                    }, {
                        label: '南湖区'
                    }, {
                        label: '海盐县'
                    }, {
                        label: '平湖市'
                    }],
                    label: '嘉兴'
                }, {
                    children: [{
                        label: '吴兴区'
                    }, {
                        label: '长兴县'
                    }, {
                        label: '安吉县'
                    }, {
                        label: '南浔区'
                    }, {
                        label: '德清县'
                    }, {
                        label: '江干区'
                    }],
                    label: '湖州'
                }, {
                    children: [{
                        label: '西湖区'
                    }, {
                        label: '余杭区'
                    }, {
                        label: '下城区'
                    }, {
                        label: '江干区'
                    }, {
                        label: '萧山区'
                    }, {
                        label: '富阳区'
                    }, {
                        label: '淳安县'
                    }, {
                        label: '建德市'
                    }, {
                        label: '桐庐县'
                    }, {
                        label: '临安市'
                    }, {
                        label: '上城区'
                    }, {
                        label: '拱墅区'
                    }, {
                        label: '滨江区'
                    }],
                    label: '杭州'
                }]
                },
                {
                    label: '广西',
                children: [{
                    children: [{
                        label: '博白县'
                    }, {
                        label: '玉州区'
                    }, {
                        label: '北流市'
                    }, {
                        label: '陆川县'
                    }, {
                        label: '兴业县'
                    }, {
                        label: '容县'
                    }],
                    label: '玉林'
                }, {
                    children: [{
                        label: '长洲区'
                    }, {
                        label: '万秀区'
                    }],
                    label: '梧州'
                }, {
                    children: [{
                        label: '钦南区'
                    }, {
                        label: '钦北区'
                    }, {
                        label: '灵山县'
                    }, {
                        label: '浦北县'
                    }],
                    label: '钦州'
                }, {
                    children: [{
                        label: '青秀区'
                    }, {
                        label: '西乡塘区'
                    }, {
                        label: '兴宁区'
                    }, {
                        label: '上林县'
                    }, {
                        label: '宾阳县'
                    }, {
                        label: '横县'
                    }, {
                        label: '马山县'
                    }, {
                        label: '良庆区'
                    }, {
                        label: '武鸣区'
                    }, {
                        label: '隆安县'
                    }, {
                        label: '邕宁区'
                    }, {
                        label: '江南区'
                    }],
                    label: '南宁'
                }, {
                    children: [{
                        label: '柳南区'
                    }, {
                        label: '融安县'
                    }, {
                        label: '城中区'
                    }, {
                        label: '鹿寨县'
                    }, {
                        label: '鱼峰区'
                    }, {
                        label: '柳江区'
                    }, {
                        label: '柳北区'
                    }, {
                        label: '柳城县'
                    }, {
                        label: '三江侗族自治县'
                    }, {
                        label: '融水苗族自治县'
                    }],
                    label: '柳州'
                }, {
                    children: [{
                        label: '合山市'
                    }, {
                        label: '武宣县'
                    }, {
                        label: '象州县'
                    }, {
                        label: '兴宾区'
                    }, {
                        label: '金秀瑶族自治县'
                    }, {
                        label: '忻城县'
                    }],
                    label: '来宾'
                }, {
                    children: [{
                        label: '富川瑶族自治县'
                    }, {
                        label: '八步区'
                    }, {
                        label: '钟山县'
                    }],
                    label: '贺州'
                }, {
                    children: [{
                        label: '巴马瑶族自治县'
                    }, {
                        label: '金城江区'
                    }, {
                        label: '罗城仫佬族自治县'
                    }, {
                        label: '南丹县'
                    }, {
                        label: '宜州市'
                    }, {
                        label: '东兰县'
                    }, {
                        label: '都安瑶族自治县'
                    }, {
                        label: '大化瑶族自治县'
                    }, {
                        label: '天峨县'
                    }, {
                        label: '环江毛南族自治县'
                    }, {
                        label: '凤山县'
                    }],
                    label: '河池'
                }, {
                    children: [{
                        label: '龙胜各族自治县'
                    }, {
                        label: '平乐县'
                    }, {
                        label: '恭城瑶族自治县'
                    }, {
                        label: '资源县'
                    }, {
                        label: '临桂区'
                    }, {
                        label: '荔浦县'
                    }, {
                        label: '象山区'
                    }, {
                        label: '叠彩区'
                    }, {
                        label: '兴安县'
                    }, {
                        label: '七星区'
                    }, {
                        label: '全州县'
                    }, {
                        label: '阳朔县'
                    }, {
                        label: '永福县'
                    }, {
                        label: '秀峰区'
                    }, {
                        label: '灵川县'
                    }, {
                        label: '灌阳县'
                    }],
                    label: '桂林'
                }, {
                    children: [{
                        label: '桂平市'
                    }, {
                        label: '港北区'
                    }, {
                        label: '覃塘区'
                    }, {
                        label: '平南县'
                    }],
                    label: '贵港'
                }, {
                    children: [{
                        label: '防城区'
                    }, {
                        label: '港口区'
                    }, {
                        label: '上思县'
                    }, {
                        label: '东兴市'
                    }],
                    label: '防城港'
                }, {
                    children: [{
                        label: '天等县'
                    }, {
                        label: '宁明县'
                    }, {
                        label: '八步区'
                    }, {
                        label: '扶绥县'
                    }, {
                        label: '龙州县'
                    }, {
                        label: '江州区'
                    }],
                    label: '崇左'
                }, {
                    children: [{
                        label: '海城区'
                    }, {
                        label: '合浦县'
                    }, {
                        label: '铁山港区'
                    }, {
                        label: '银海区'
                    }],
                    label: '北海'
                }, {
                    children: [{
                        label: '田阳县'
                    }, {
                        label: '右江区'
                    }, {
                        label: '靖西市'
                    }, {
                        label: '田林县'
                    }, {
                        label: '乐业县'
                    }, {
                        label: '德保县'
                    }, {
                        label: '平果县'
                    }, {
                        label: '田东县'
                    }, {
                        label: '凌云县'
                    }],
                    label: '百色'
                }]
                },
                {
                    label: '云南',
                children: [{
                    children: [{
                        label: '官渡区'
                    }, {
                        label: '五华区'
                    }],
                    label: '昆明'
                }]
                },
                {
                    label: '内蒙古',
                children: [{
                    children: [{
                        label: '锡林浩特市'
                    }, {
                        label: '苏尼特左旗'
                    }, {
                        label: '正蓝旗'
                    }, {
                        label: '西乌珠穆沁旗'
                    }, {
                        label: '阿巴嘎旗'
                    }, {
                        label: '多伦县'
                    }, {
                        label: '苏尼特右旗'
                    }, {
                        label: '镶黄旗'
                    }, {
                        label: '太仆寺旗'
                    }, {
                        label: '东乌珠穆沁旗'
                    }, {
                        label: '二连浩特市'
                    }],
                    label: '锡林浩特'
                }, {
                    children: [{
                        label: '科尔沁右翼前旗'
                    }, {
                        label: '乌兰浩特市'
                    }, {
                        label: '突泉县'
                    }, {
                        label: '科尔沁右翼中旗'
                    }, {
                        label: '扎赉特旗'
                    }, {
                        label: '回民区'
                    }],
                    label: '乌兰浩特'
                }, {
                    children: [{
                        label: '海勃湾区'
                    }, {
                        label: '海南区'
                    }, {
                        label: '乌达区'
                    }],
                    label: '乌海'
                }, {
                    children: [{
                        label: '科尔沁区'
                    }, {
                        label: '霍林郭勒市'
                    }, {
                        label: '科尔沁左翼后旗'
                    }, {
                        label: '奈曼旗'
                    }, {
                        label: '开鲁县'
                    }, {
                        label: '扎鲁特旗'
                    }, {
                        label: '回民区'
                    }],
                    label: '通辽'
                }, {
                    children: [{
                        label: '临河区'
                    }, {
                        label: '五原县'
                    }, {
                        label: '杭锦后旗'
                    }, {
                        label: '乌拉特前旗'
                    }, {
                        label: '乌拉特后旗'
                    }, {
                        label: '磴口县'
                    }],
                    label: '临河'
                }, {
                    children: [{
                        label: '集宁区'
                    }, {
                        label: '化德县'
                    }, {
                        label: '凉城县'
                    }, {
                        label: '丰镇市'
                    }, {
                        label: '兴和县'
                    }, {
                        label: '商都县'
                    }],
                    label: '集宁'
                }, {
                    children: [{
                        label: '新城区'
                    }, {
                        label: '土默特左旗'
                    }, {
                        label: '清水河县'
                    }, {
                        label: '赛罕区'
                    }, {
                        label: '玉泉区'
                    }, {
                        label: '回民区'
                    }, {
                        label: '武川县'
                    }],
                    label: '呼和浩特'
                }, {
                    children: [{
                        label: '鄂温克族自治旗'
                    }, {
                        label: '海拉尔区'
                    }, {
                        label: '牙克石市'
                    }, {
                        label: '满洲里市'
                    }, {
                        label: '根河市'
                    }, {
                        label: '扎兰屯市'
                    }, {
                        label: '额尔古纳市'
                    }, {
                        label: '新巴尔虎右旗'
                    }, {
                        label: '扎赉诺尔区'
                    }, {
                        label: '莫力达瓦达斡尔族自治旗'
                    }],
                    label: '海拉尔'
                }, {
                    children: [{
                        label: '乌审旗'
                    }, {
                        label: '达拉特旗'
                    }, {
                        label: '康巴什区'
                    }, {
                        label: '鄂托克旗'
                    }, {
                        label: '东胜区'
                    }, {
                        label: '伊金霍洛旗'
                    }, {
                        label: '杭锦旗'
                    }, {
                        label: '准格尔旗'
                    }, {
                        label: '鄂托克前旗'
                    }],
                    label: '鄂尔多斯'
                }, {
                    children: [{
                        label: '红山区'
                    }, {
                        label: '巴林左旗'
                    }, {
                        label: '松山区'
                    }, {
                        label: '元宝山区'
                    }, {
                        label: '克什克腾旗'
                    }, {
                        label: '喀喇沁旗'
                    }, {
                        label: '翁牛特旗'
                    }, {
                        label: '林西县'
                    }, {
                        label: '敖汉旗'
                    }, {
                        label: '宁城县'
                    }],
                    label: '赤峰'
                }, {
                    children: [{
                        label: '青山区'
                    }, {
                        label: '昆都仑区'
                    }, {
                        label: '达尔罕茂明安联合旗'
                    }, {
                        label: '九原区'
                    }, {
                        label: '东河区'
                    }, {
                        label: '石拐区'
                    }],
                    label: '包头'
                }, {
                    children: [{
                        label: '阿拉善左旗'
                    }],
                    label: '阿拉善盟'
                }]
                },
                {
                    label: '辽宁',
                children: [{
                    children: [{
                        label: '沈河区'
                    }],
                    label: '沈阳'
                }, {
                    children: [{
                        label: '兴隆台区'
                    }, {
                        label: '大洼区'
                    }],
                    label: '盘锦'
                }, {
                    children: [{
                        label: '新抚区'
                    }],
                    label: '抚顺'
                }, {
                    children: [{
                        label: '铁西区'
                    }],
                    label: '鞍山'
                }]
                },
                {
                    label: '广东',
                children: [{
                    children: [{
                        label: '香洲区'
                    }, {
                        label: '斗门区'
                    }, {
                        label: '金湾区'
                    }],
                    label: '珠海'
                }, {
                    children: [{
                        label: '中山市'
                    }],
                    label: '中山'
                }, {
                    children: [{
                        label: '端州区'
                    }, {
                        label: '四会市'
                    }, {
                        label: '鼎湖区'
                    }, {
                        label: '广宁县'
                    }, {
                        label: '怀集县'
                    }],
                    label: '肇庆'
                }, {
                    children: [{
                        label: '赤坎区'
                    }, {
                        label: '吴川市'
                    }, {
                        label: '廉江市'
                    }, {
                        label: '徐闻县'
                    }, {
                        label: '霞山区'
                    }, {
                        label: '麻章区'
                    }, {
                        label: '遂溪县'
                    }, {
                        label: '雷州市'
                    }],
                    label: '湛江'
                }, {
                    children: [{
                        label: '罗定市'
                    }, {
                        label: '云安区'
                    }, {
                        label: '新兴县'
                    }, {
                        label: '郁南县'
                    }],
                    label: '云浮'
                }, {
                    children: [{
                        label: '阳春市'
                    }, {
                        label: '江城区'
                    }],
                    label: '阳江'
                }, {
                    children: [{
                        label: '罗湖区'
                    }, {
                        label: '南山区'
                    }, {
                        label: '龙岗区'
                    }, {
                        label: '盐田区'
                    }, {
                        label: '福田区'
                    }, {
                        label: '宝安区'
                    }],
                    label: '深圳'
                }, {
                    children: [{
                        label: '浈江区'
                    }, {
                        label: '乐昌市'
                    }, {
                        label: '南雄市'
                    }, {
                        label: '乳源瑶族自治县'
                    }, {
                        label: '新丰县'
                    }, {
                        label: '武江区'
                    }],
                    label: '韶关'
                }, {
                    children: [{
                        label: '城区'
                    }, {
                        label: '海丰县'
                    }, {
                        label: '陆丰市'
                    }, {
                        label: '陆河县'
                    }],
                    label: '汕尾'
                }, {
                    children: [{
                        label: '金平区'
                    }, {
                        label: '澄海区'
                    }, {
                        label: '南澳县'
                    }, {
                        label: '龙湖区'
                    }, {
                        label: '潮阳区'
                    }, {
                        label: '潮南区'
                    }],
                    label: '汕头'
                }, {
                    children: [{
                        label: '清城区'
                    }, {
                        label: '佛冈县'
                    }, {
                        label: '连州市'
                    }, {
                        label: '清新区'
                    }, {
                        label: '英德市'
                    }],
                    label: '清远'
                }, {
                    children: [{
                        label: '梅江区'
                    }, {
                        label: '兴宁市'
                    }, {
                        label: '五华县'
                    }, {
                        label: '梅县区'
                    }, {
                        label: '丰顺县'
                    }, {
                        label: '大埔县'
                    }],
                    label: '梅州'
                }, {
                    children: [{
                        label: '电白区'
                    }, {
                        label: '信宜市'
                    }, {
                        label: '高州市'
                    }, {
                        label: '化州市'
                    }, {
                        label: '茂南区'
                    }],
                    label: '茂名'
                }, {
                    children: [{
                        label: '普宁市'
                    }, {
                        label: '榕城区'
                    }, {
                        label: '揭西县'
                    }],
                    label: '揭阳'
                }, {
                    children: [{
                        label: '蓬江区'
                    }, {
                        label: '开平市'
                    }, {
                        label: '台山市'
                    }, {
                        label: '恩平市'
                    }, {
                        label: '新会区'
                    }],
                    label: '江门'
                }, {
                    children: [{
                        label: '惠阳区'
                    }, {
                        label: '龙门县'
                    }, {
                        label: '惠城区'
                    }, {
                        label: '惠东县'
                    }, {
                        label: '博罗县'
                    }],
                    label: '惠州'
                }, {
                    children: [{
                        label: '源城区'
                    }, {
                        label: '东源县'
                    }, {
                        label: '和平县'
                    }, {
                        label: '龙川县'
                    }, {
                        label: '连平县'
                    }],
                    label: '河源'
                }, {
                    children: [{
                        label: '越秀区'
                    }, {
                        label: '天河区'
                    }, {
                        label: '黄埔区'
                    }, {
                        label: '番禺区'
                    }, {
                        label: '荔湾区'
                    }, {
                        label: '海珠区'
                    }, {
                        label: '南沙区'
                    }, {
                        label: '白云区'
                    }, {
                        label: '增城区'
                    }, {
                        label: '花都区'
                    }],
                    label: '广州'
                }, {
                    children: [{
                        label: '顺德区'
                    }, {
                        label: '南海区'
                    }, {
                        label: '禅城区'
                    }, {
                        label: '高明区'
                    }, {
                        label: '三水区'
                    }],
                    label: '佛山'
                }, {
                    children: [{
                        label: '东莞市'
                    }],
                    label: '东莞'
                }, {
                    children: [{
                        label: '饶平县'
                    }, {
                        label: '潮安区'
                    }, {
                        label: '湘桥区'
                    }],
                    label: '潮州'
                }]
                },
                {
                    label: '青海',
                children: [{
                    children: [{
                        label: '曲麻莱县'
                    }, {
                        label: '杂多县'
                    }, {
                        label: '称多县'
                    }, {
                        label: '玉树市'
                    }, {
                        label: '治多县'
                    }, {
                        label: '囊谦县'
                    }],
                    label: '玉树'
                }, {
                    children: [{
                        label: '城西区'
                    }, {
                        label: '城中区'
                    }, {
                        label: '城东区'
                    }, {
                        label: '湟源县'
                    }, {
                        label: '城北区'
                    }, {
                        label: '湟中县'
                    }],
                    label: '西宁'
                }, {
                    children: [{
                        label: '门源回族自治县'
                    }, {
                        label: '刚察县'
                    }],
                    label: '门源'
                }, {
                    children: [{
                        label: '同仁县'
                    }, {
                        label: '尖扎县'
                    }, {
                        label: '泽库县'
                    }],
                    label: '黄南'
                }, {
                    children: [{
                        label: '海晏县'
                    }],
                    label: '海晏'
                }, {
                    children: [{
                        label: '化隆回族自治县'
                    }, {
                        label: '平安区'
                    }, {
                        label: '循化撒拉族自治县'
                    }, {
                        label: '互助土族自治县'
                    }, {
                        label: '乐都区'
                    }, {
                        label: '民和回族土族自治县'
                    }],
                    label: '海东'
                }, {
                    children: [{
                        label: '久治县'
                    }, {
                        label: '玛沁县'
                    }, {
                        label: '玛多县'
                    }, {
                        label: '班玛县'
                    }, {
                        label: '甘德县'
                    }],
                    label: '果洛'
                }, {
                    children: [{
                        label: '贵德县'
                    }, {
                        label: '共和县'
                    }],
                    label: '共和'
                }, {
                    children: [{
                        label: '格尔木市'
                    }, {
                        label: '天峻县'
                    }],
                    label: '格尔木'
                }, {
                    children: [{
                        label: '德令哈市'
                    }],
                    label: '德令哈'
                }]
                },
                {
                    label: '北京',
                children: [{
                    children: [{
                        label: '丰台区'
                    }, {
                        label: '东城区'
                    }, {
                        label: '大兴区'
                    }, {
                        label: '朝阳区'
                    }, {
                        label: '通州区'
                    }, {
                        label: '海淀区'
                    }, {
                        label: '顺义区'
                    }, {
                        label: '密云区'
                    }, {
                        label: '怀柔区'
                    }, {
                        label: '石景山区'
                    }, {
                        label: '门头沟区'
                    }, {
                        label: '房山区'
                    }, {
                        label: '延庆区'
                    }, {
                        label: '平谷区'
                    }, {
                        label: '昌平区'
                    }, {
                        label: '西城区'
                    }],
                    label: '北京'
                }]
            }
          ], {
              depth: 3,
              defaultValue: [0, 1, 1],
              // onChange: function (result) {
              //     console.log(result);
              // },
              onConfirm: function (city) {
                console.log(city)
                  let num1 = city[1].label,
                      num2 = city[2].label;
                  // Bus.$emit('val', num[2].value);
                  Bus.$emit('val', num2)
                  sessionStorage.setItem("item1",num1)
                  sessionStorage.setItem("item2",num2)
              },
              id: 'cascadePicker'
          });
      // Cookies.set("city2",window.CITYNAME)
      // Cookies.set("city1",window.LONGITUDE)
      // Cookies.set("city",window.LATITUDE)
      // this.$router.push({
      //   path: "/city",
      //   query: {
      //       params: this.cityName1,
      //       params2: this.cityName2
      //     }
      // });
    },
    aginEnter() {
      // alert(33)
      this.SHOWLOADING(true);
      this.initScroll();
      let that = this;
      if (this.cityName1 && this.cityName1 != "定位中" && window.LATITUDE) {
        this.init();
      } else {
        try {
          new BMap.Geolocation().getCurrentPosition(function(r) {
            if (this.getStatus() == BMAP_STATUS_SUCCESS) {
              // 判断状态
              let pt = r.point;
              console.log(r);

              new BMap.Geocoder().getLocation(pt, function(rs) {
                // if (this.getStatus() == BMAP_STATUS_SUCCESS) {
                if (rs.point) {
                  let addComp = rs.addressComponents;
                  window.LATITUDE = rs.point.lat;
                  window.LONGITUDE = rs.point.lng;
                  window.CITYNAME = addComp.city;
                  that.cityName1 = addComp.city;
                } else {
                  window.LATITUDE = r.point.lat;
                  window.LONGITUDE = r.point.lng;
                  window.CITYNAME = r.address.city;
                  that.cityName1 = r.address.city;
                }
                that.init();
                // alert(addComp.province + addComp.city + addComp.district + addComp.street + addComp.streetNumber);
              });
            } else {
              this.SHOWLOADING(true);
            }
          });
        } catch (e) {}
      }
    },
    intervalCity() {
      this.cityName1 = window.CITYNAME || "定位中";
    },
    defaultIconF(i) {
      this.shopList[i].PIC_URL_1 = "/static/img/error.png";
    },
    // GetDistance(a, b, c, d) {
    //   // alert(GetDistance(a, b, c, d))
    //   return GetDistance(a, b, c, d);
    // },
    detail(url) {
      window.location = url;
    },
    gotoAddress(path) {
      this.$router.push(path);
    },
    goLogin(e) {
      let urls = "https://find.cmpay.com:9102/rcServer/hbopenreceive?state=" + window.location.href;
      // console.log(urls);
      window.location = urls;
    },
    done() {
      this.$router.push("/mine");
    },
    touchStart(e) {
      let touch = e.changedTouches[0];
      this.startX = touch.pageX;
      // console.log(touch)
      // startY = touch.pageY;
      // startX = touch.pageX;
    },
    goDetail(event, obj, flag, channel = "default") {
      // alert(33)
      // if (flag == 2) {
      //   let touch = event.changedTouches[0];
      //   console.log(touch);
      //   this.endX = touch.pageX;
      //   // 点击影响滑动
      //   if (Math.abs(this.endX - this.startX) > 50) {
      //     return;
      //   }
      // }

      // let touch = event.touches[0];
      // event.stopPropagation();
      // event.preventDefault();
      // if(this.stopClick){
      //   return
      // }
      // 防止pc端触发两次点击
      // if (!event._constructed) {
      //   return;
      // }
      //埋点 parent_title, sub_title,phone,remark, session

      let url = flag == 1 ? obj.MERC_URL : obj.marketingEventCotent;
      // if (flag == 1) {
      //   setLItem("shopList", JSON.stringify(this.shopList));
      //   // localStorage.setItem("shopList", JSON.stringify(this.shopList));
      //   //.replace(/www/, "uat")
      //   this.SHOWLOADING(true);
      //   // setTimeout(()=>{

      //   // },5000)
      //   // this.stopClick = 1
      //   window.location =
      //     url.indexOf("?") > 0
      //       ? url.replace(/\?/, "?showtitle=false&") +
      //         "&SOURCE=DISCOVER&" +
      //         window.location.href.split("?")[1].split("#")[0]
      //       : url +
      //         "?showtitle=false&SOURCE=DISCOVE&" +
      //         window.location.href.split("?")[1].split("#")[0];
      //   return;
      // }
      // alert(33)
      if(isHebaoApp()) {
        if (
          (/iP(ad|hone|od)/.test(navigator.userAgent) ? "ios" : "android") ==
          "android"
        ) {
          // window.location =
          //   url.indexOf("?") > 0
          //     ? url.replace(/\?/, "?showtitle=false&") +
          //       "&SOURCE=DISCOVER&" +
          //       window.location.href.split("?")[1].split("#")[0]
          //     : url +
          //       "?showtitle=false&SOURCE=DISCOVE&" +
          //       window.location.href.split("?")[1].split("#")[0];
          // window.location = url.replace(/\?/, "?showtitle=false&");
          if (flag == 2) {
            let url2 =
              url.indexOf("?") > 0
                ? url.replace(
                    /\?/,
                    "?hebaosso=true&SOURCE=DISCOVER&account=" +
                      this.token.productNo +
                      "&"
                  )
                : url +
                  "?hebaosso=true&SOURCE=DISCOVER&account=" +
                  this.token.productNo;
            // console.log(url2);
            window.goActivity.goWeb(url2);
          } else {
            window.goActivity.goWeb(
              url.replace(
                /\?/,
                "?showtitle=false&hebaosso=true&SOURCE=DISCOVER&account=" +
                  this.token.productNo +
                  "&"
              )
            );
          }
        } else {
          // console.log(
          //   "activity://goWeb?url=" + url.replace(/\?/, "?showtitle=false&")
          // );
          if (flag == 2) {
            let url_2 =
              url.indexOf("?") > 0
                ? url.replace(
                    /\?/,
                    "?hebaosso=true&SOURCE=DISCOVER&account=" +
                      this.token.productNo +
                      "&"
                  )
                : url +
                  "?hebaosso=true&SOURCE=DISCOVER&account=" +
                  this.token.productNo;
            // console.log(url_2);
            window.location = "activity://goWeb?url=" + url_2;
          } else {
            // console.log(
            //   url.replace(/\?/, "?showtitle=false&hebaosso=true&SOURCE=DISCOVER&")
            // );
            window.location =
              "activity://goWeb?url=" +
              url.replace(
                /\?/,
                "?showtitle=false&hebaosso=true&SOURCE=DISCOVER&account=" +
                  this.token.productNo +
                  "&"
              );
          }
        }
      } else {
          window.location.href="" + obj.marketingEventCotent
      }
    },
    filterObj(obj) {
      for (let i = 0; i < obj.length; i++) {
        if (obj[i].PIC_URL_1) {
          obj[i].PIC_URL_1 = this.baseImg + obj[i].PIC_URL_1;
        }
      }
      return obj;
    },
    loadMore() {
      if (this.shopListFlag) {
        return;
      }
      this.CURRENTPAGE += 1;

      this.l=sessionStorage.getItem("item1");
      this.s=sessionStorage.getItem("item2");
      // this.l=(document.cookie.indexOf("item")!=-1)?JSON.parse(Cookies.get("item")).city_longitude:window.LONGITUDE
      // this.s=(document.cookie.indexOf("item")!=-1)?JSON.parse(Cookies.get("item")).city_latitude:window.LATITUDE
      // this.cityName1 =((document.cookie.indexOf("item")!=-1)?JSON.parse(Cookies.get("item")).city_name:"")||window.CITYNAME || "定位中";
      axios.post("merchants/getMerchantsInfo", {
        cityName: this.l || '',
        areaName:  this.s || '',
        longitude: window.LONGITUDE, // 经度
        latitude: window.LATITUDE, // 维度
        currentPage: this.CURRENTPAGE,
        pagNum: this.PAGNUM || 15,
        // session: this.token.session.replace(/\+/g, "%2B"),
        // map_type: window.isUseBaiDuLoc ? 0 : 0,
        // map_type: window.isUseBaiDuLoc,
        }).then(res => {
          // this.shopList = res.STORES_REC;
          // 合并数组
          this.shopList.push.apply(this.shopList, this.filterObj(res.data));
          if (res.data.length < this.PAGNUM) {
            this.shopListFlag = true;
            this.data1 = true;
            // 数组没有更多了
          } else {
            this.shopListFlag = false;
            this.data1 = false;
          }
          this.initScroll();
        })
        .catch(res => {
          this.initScroll();
        });
    },
    scrollTo() {
      this.$refs.scroll.scrollTo(
        this.scrollToX,
        this.scrollToY,
        this.scrollToTime,
        ease[this.scrollToEasing]
      );
    },
    onPullingDown() {
      // this.init(true);
      // 模拟更新数据
      // setTimeout(() => {
      //   if (Math.random() > 0.5) {
      //     // 如果有新数据
      //     this.items.unshift(this.$i18n.t('normalScrollListPage.newDataTxt') + +new Date())
      //   } else {
      //     // 如果没有新数据
      //     this.$refs.scroll.forceUpdate()
      //   }
      // }, 2000)
    },
    onPullingUp() {
      // 更新数据
      // console.log('pulling up and load data')
      // setTimeout(() => {
      //   if (Math.random() > 0.5) {
      //     // 如果有新数据
      //     let newPage = []
      //     for (let i = 0; i < 10; i++) {
      //       newPage.push(this.$i18n.t('normalScrollListPage.previousTxt') + ++this.itemIndex + this.$i18n.t('normalScrollListPage.followingTxt'))
      //     }

      //     this.items = this.items.concat(newPage)
      //   } else {
      //     // 如果没有新数据
      //     this.$refs.scroll.forceUpdate()
      //   }
      // }, 1500)
      this.loadMore();
    },
    rebuildScroll() {
      this.nextTick(() => {
        this.$refs.scroll.destroy();
        this.$refs.scroll.initScroll();
      });
    },

    refresh() {
      this.init(true);
      // 下拉刷新
      // if (this.refTime) {
      //   // 移除定时器
      //   clearTimeout(this.refTime);
      //   this.init(true);
      //   // window.location = "http://211.138.236.219:9100/main.html?hebaosso=true&showtitle=false"
      // }
    },
    init(flag) {

      this.CURRENTPAGE = 1;
      if (!flag) {
        this.SHOWLOADING(true);
      } else {
        this.SHOWLOADING(false);
      }
      // 单点登录
      // 请求banner1
      // this.cityName1 =
      //   window.CITYNAME ||
      //   (/iP(ad|hone|od)/.test(navigator.userAgent)
      //     ? window.LBSINFO.CityName || "定位中"
      //     : getCode(window.LBSINFO.CityName));

      // if (!window.LONGITUDE) {
      //   this.SHOWLOADING(false);
      //   return;
      // }
      this.l=sessionStorage.getItem("item1");
      this.s=sessionStorage.getItem("item2");
      // this.cityName1 =((document.cookie.indexOf("item")!=-1)?JSON.parse(Cookies.get("item")).city_name:"")||window.CITYNAME || "定位中";
      // return;
      axios.post("merchants/getMerchantsInfo", {
        cityName: this.l || '',
        areaName:  this.s || '',
        longitude: window.LONGITUDE, // 经度
        latitude: window.LATITUDE, // 维度
        currentPage: this.CURRENTPAGE,
        // mblno: this.token.productNo || "", //用户手机号
        pagNum: this.PAGNUM || 15,
        // session: this.token.session.replace(/\+/g, "%2B"),
        // map_type: window.isUseBaiDuLoc ? 0 : 0,
        // map_type: window.isUseBaiDuLoc,
        }).then(res => {
          if (res.data && res.data.length > 0) {
            this.isError = true;
            this.shopList = this.filterObj(res.data);
            // if (flag) {
            //   this.home.finishPullDown();
            // } else {
            //   this.home.refresh();
            // }
            this.initScroll();
            setTimeout(() => {
              this.SHOWLOADING(false);
            }, 300);
          } else {
            this.isError = true;
            this.totalInit++;
            if (this.totalInit <= 5) {
              this.init();
            } else {
              // this.shopList = getLItem("shopList", 600000);
              this.shopList = [];
              // setTimeout(() => {
              this.SHOWLOADING(false);
              this.isError = false;
              if (flag) {
                this.initScroll();
              } else {
                this.initScroll();
              }
              // }, 300);
            }
            return;
          }

          // console.log(this.shopList);
          if (res.data.length < this.PAGNUM) {
            this.shopListFlag = true;
            this.data1 = true;
            //没有更多了
          } else {
            this.shopListFlag = false;
            this.data1 = false;
          }
          // setTimeout(() => {
          //   // this.foodsScroll.scrollTo(0, -42, 500);
          //   this.home.refresh();
          //   this._calcHeight();
          // }, 40);
          // alert(33)
        })
        .catch(res => {
          // alert(11)
          // setTimeout(()=>{
          this.SHOWLOADING(false);
          this.totalInit++;
          if (this.totalInit <= 5) {
            this.init();
          } else {
            this.initScroll();
          }

          // },300)
        });
    },
    initScroll() {
      // if (!this.home) {
      // this.$nextTick(() => {
      // this.foodsScroll = this.home = new BScroll(this.$refs.home, {
      //   scrollY: true,
      //   // startY: -39,
      //   // scrollX: false,
      //   click: true,
      //   probeType: 1,
      //   bounce: true,
      //   scrollbar: {
      //     fade: true,
      //     interactive: false // 1.8.0 新增
      //   },
      //   //  pullUpLoad:{
      //   //                   threshold: -70, // 当上拉到超过底部 4.375rem 时，
      //   //               }
      //   // pullDownRefresh: {
      //   //   threshold: 70,
      //   //   stop: 50 // 在上拉到超过底部 1.25rem 时，触发 pullingUp 事件
      //   // },
      //   pullUpLoad: {
      //     threshold: 50 // 在上拉到超过底部 1.25rem 时，触发 pullingUp 事件
      //   }
      //   // momentumLimitDistance: 15
      // });
      // // this.foodsScroll.on("pullingDown", () => {
      // //   this.refresh(true);
      // // });
      // this.foodsScroll.on("pullingUp", pos => {
      //   this.loadMore();
      // });
      // });
      // this.foodsScroll.on('scrollEnd',(pos)=>{
      //   console.log("888888888")
      //   console.log(pos)
      // })
      // } else {
      // this.home.finishPullUp();
      // setTimeout(() => {
      // this.$refs.scroll.forceUpdate()
      // this.home.finishPullDown();
      // // }, 300);
      // this.home.finishPullUp();
      // this.home.refresh();
      // }
    },
    _calcHeight() {
      // if (this.residue > 0) {
      //   this.listHeight[this.listHeight.length - 1] += residue;
      // }
      // this.residueHeight = residue > 0 ? residue + "px" : "0rem"; //设置最后菜单的padding
      // 重置滚动区域
      // setTimeout(() => {
      //   this._initScroll();
      // }, 0);
    },
    goBack() {
      // this.$router.go(-1);
      this.$router.push("/my")
    }
  },
  activated(){
    console.log(2323)
    // alert()
    this.refreshNow = !this.refreshNow
  },
  watch:{
    latitude(curVal,oldVal){
      this.init()
      if(curVal&&curVal!="" && this.shopList.length<=0){
      }

    },
    cityName1(curVal,oldVal){
      // this.cityName1 = curVal;
      // this.flagS=false
      this.init()
      if(curVal !== oldVal){

      }
    },
  }
  // props:['activeIcon']
};
</script>

<style lang="scss" scoped>
@import "~@@/style/mixin";
// .home{
//   position: relative;
// }
.nullHeight {
  height: 0.5625rem;
  background: #f6f7f8;
}
.refresh {
  text-align: center;
  padding: 1.25rem 0;
  color: #999999;
  background-image: url("/static/img/refresh.gif");
  background-size: 40%;
  background-position: 50% 50%;
  background-repeat: no-repeat;
  padding-top: 6.875rem;
  position: absolute;
  top: 3.125rem;
  width: 100%;
  height: 3.125rem;
}
.home {
  // transition: all 0.3s;
  // -webkit-transition: all 0.3s;
  // transform: translateZ(0);
  // -webkit-transform: translateZ(0);
  position: absolute;
  z-index: 1;
  top: 3rem;
  left: 0;
  width: 100%;
  // position: relative;
  height: 100%;
  padding-top: 3rem;
  padding-bottom: 2rem;
  // background: #f0f1f2;
  overflow: hidden;
}
.home1 {
  // margin-top: 4.25rem;
  height: 100%;
  overflow: hidden;
  // z-index: 19;
}
.content {
  // height: auto;
}
.s {
  position: relative;
  // margin-top: .625rem;
  // background: #ffffff;
}
.s_8 {
  @include wh(100%, 3rem);
  // padding-top: 1.25rem;
}
header {
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
    height: 3rem;
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

  .lt {
    height: 100%;
    line-height: 3.125rem;
    font-size: 1.125rem;
    font-family: PingFangSC-Regular;
    color: #fff;
    padding-right: 3.5rem;
    text-align: center;
  }
  .ex {
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
.s_1,
.s_5_1 {
  // @include wh(100%, 3rem);
  // // padding-top: 1.25rem;
  // background: #ffffff;
  // position: fixed;
  // z-index: 100000;
  @include wh(100%, 3rem);
  // padding-top: 1.25rem;
  background: #ffffff;
  position: sticky;
  z-index: 100;
  top: 3rem;
  left: 0;
  .cat_w {
    height: 3rem !important;
    top: 0rem !important;
    background: white;
    line-height: 3rem;
    display: -webkit-box;
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex;
  }
  .cat {
    width: 33%;
    text-align: center;
    -webkit-box-flex: 1;
    -webkit-flex: 1;
    -ms-flex: 1;
    flex: 1;
    height: 3rem;
    line-height: 3rem;
    background: #fff;
    font-family: PingFangSC-Regular;
    color: #13252e;
    font-size: 0.9375rem;
  }
  .cat.active {
    background: #fff;
    color: #ed1991;
    border-bottom: 0.125rem solid #ed1991;
    border-radius: 0;
  }
  .title {
    position: relative;
    height: 1.5625rem;
    text-align: center;
  }
  .title:after {
    @include onepx1(#d8d8d8);
  }
  .text {
    position: relative;
    width: 4rem;
    padding: 0.3125rem 0.625rem;
    top: 0.75rem;
    background: #fff;
    z-index: 10;
    color: #444444;
    font-size: 0.875rem;
  }
  ul {
    width: 100%;
    height: 100%;
  }
  .l {
    height: 100%;
    line-height: 3rem;
  }
  .t {
    max-width: 7rem;
    color: #6c6c6c;
    font-size: 0.8125rem;
    position: relative;
    float: left;
    text-align: left;
    padding-left: 0.9375rem;
    padding-right: 1rem;
    background: url(/static/img/down.png) no-repeat 100% 50%;
    background-size: .8125rem;
    // padding-right: 0.6rem;
    @include space();
  }
  // .t::after {
  //   content: " ";
  //   width: 0.625rem;
  //   height: 0.625rem;
  //   display: block;
  //   position: absolute;
  //   background-image: url(/static/img/1-13.png);
  //   background-repeat: no-repeat;
  //   background-position: 50%;
  //   background-size: 0.625rem auto;
  //   top: 1.05rem;
  //   right: 0.4375rem;
  // }
  .icon {
    display: block;
    width: 1rem;
    height: 2rem;
    position: absolute;
    left: 0.25rem;
    // top: 0.9875rem;
    img {
      width: 100%;
      display: block;
      position: relative;
      top: 0.5rem;
    }
  }
  .i {
    width: auto;
    margin-left: 3.125rem;
    display: block;
    margin-right: 1.25rem;
    position: relative;
    padding-left: 0.5rem;
    button {
      display: block;
      height: 1.9375rem;
      width: 100%;
      z-index: 999;
      position: relative;
      top: 0.5rem;
      text-indent: 2em;
      line-height: 1.9375rem;
      text-align: left;
      color: #a7a7a7;
      background: #f0f1f2;
      border-radius: 0.25rem;
      font-size: 0.8125rem;
    }
  }
}
.s_2 {
  @include wh(100%, 10.625rem);
  overflow: hidden;
  background-color: #fff;
  ul,
  li {
    height: 100%;
    width: 100%;
  }
  img {
    width: 100%;
  }
  // background-image:url('/static/img/banner.jpg');
  // background-repeat: no-repeat;
  // background-position: 50%;
  // background-size: 100% auto;
}

.s_3 {
  @include wh(100%, 4.875rem);
  text-align: center;
  background-color: #fff;
  .icon {
    height: 1.75rem;
    width: auto;
    display: inline-block;
    padding: 0.84375rem 0 0.25rem 0;
  }
  .text {
    display: block;
    color: #7e7e7e;
    font-size: 0.75rem;
  }
  a.active {
    font-family: PingFangSC-Semibold;
    display: block;
    color: #13252e;
    font-size: 0.75rem;
  }

  ul {
    display: flex;
    height: 100%;
    li {
      height: 100%;
      flex: 1;
    }
  }
}
.s_4 {
  @include wh(100%, 7.125rem);
  padding-top: 0.25rem;
  padding-bottom: 0.625rem;
  overflow: hidden;
  img {
    width: 100%;
    height: auto;
    max-height: 6.125rem;
  }
}

.s_5 {
  // padding: 0 0.9375rem;
  // background: #fff;
  // margin-top: 0.5625rem;
  // margin-bottom: 1rem;
  // height: 56.25rem;
  ul {
    height: 7.5rem;
    padding-top: 1.625rem;
    position: relative;
    margin: 0 0.9375rem;
  }
  // ul::after {
  //   @include onepx1(#d8d8d8);
  // }
  ul:nth-last-child(1)::after {
    height: 0 !important;
  }
  .title {
    position: relative;
    height: 1.5625rem;
    text-align: center;
    background-color: #fff;
  }
  .title:after {
    @include onepx1(#d8d8d8);
  }
  .text {
    position: relative;
    width: 4rem;
    padding: 0.3125rem 0.625rem;
    top: 0.75rem;
    background: #fff;
    z-index: 10;
    color: #444444;
    font-size: 0.875rem;
  }
  .left {
    width: 5.1875rem;
    height: auto;
    float: left;
    max-height: 5rem;
    overflow: hidden;
    img {
      width: 100%;
      max-height: 5rem;
      min-height: 4.5625rem;
    }
  }
  .right {
    float: left;
    padding-left: 0.5625rem;
    min-width: 60%;
    width: 75%;
    text-align: left;
  }
  .c1 {
    font-size: 0.8125rem;
    color: #13252e;
    max-width: 100%;
    @include space();
  }
  .c2 {
    font-size: 0.6875rem;
    color: #999999;
    padding-top: 0.5625rem;
    max-width: 90%;
    @include space();
    .l {
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
      display: inline-block;
      max-width: 70%;
    }
  }
  .c3 {
    font-size: 0.75rem;
    color: #e11a2f;
    padding-top: 0.5625rem;
    letter-spacing: -0.00375rem;
    max-width: 70%;
    @include space();
    div {
      @include space();
    }
  }
  .r {
    position: absolute;
    right: 0.9375rem;
  }
  .b {
    display: inline-block;
    background: #fff0f1;
    border: thin solid rgba(224, 24, 45, 0.2);
    border-radius: 0.125rem;
    padding: 0.05rem 0.225rem;
    margin-right: 0.1875rem;
  }
}

.s_6 {
  padding: 0 0.9375rem;
  background: #fff;
  margin-top: 0.5625rem;
  ul {
    // height: 7.5rem;
    // padding-top: 1.625rem;
  }
  ul:nth-of-type(1) {
    padding-top: 1.625rem;
  }
  .title {
    position: relative;
    height: 1.5625rem;
    text-align: center;
  }
  .title:after {
    @include onepx1(#d8d8d8);
  }
  .text {
    position: relative;
    width: 4rem;
    padding: 0.3125rem 0.5rem;
    top: 0.75rem;
    background: #fff;
    z-index: 10;
    color: #444444;
    font-size: 0.875rem;
  }
  img {
    width: 100%;
    height: auto;
  }
  .c-t {
    color: #13252e;
    font-size: 0.9375rem;
    padding-bottom: 0.9375rem;
  }
}
.slider-wrapper {
  width: 100%;
  position: relative;
  overflow: hidden;
  a {
    display: inline-block;
    height: 100%;
  }
  img {
    max-height: 10.625rem;
  }
}
.scroll {
  height: 10.625rem;
}
.slide-content {
  -webkit-box-flex: 1;
  -webkit-flex: 1;
  flex: 1;
  position: relative;
  height: 56.25rem;
  // margin: 0 .625rem .625rem;
}
.slider-item {
  height: 100% !important;
}
.food-wrapper-banner {
  width: 100%;
  display: flex;
  flex-direction: column;
  height: calc(100% - 2.75rem);
  overflow: hidden;
  -webkit-overflow-scrolling: touch;
}
.foods-wrapper {
  width: 100%;
  height: 10.625rem;
  // padding-left: 0.9375rem;
  // padding-right: 0.9375rem;
  .title {
    height: 2.8125rem;
    font-size: 0.9375rem;
    color: #929292;
    position: relative;
    line-height: 2.8125rem;
    padding-left: 0.95rem;
    padding-right: 0.16rem;
  }
  .icon {
    height: 1.0625rem;
    width: 1.0625rem;
    vertical-align: middle;
    position: absolute;
    //left: -.3125rem;
    left: 0;
    top: 0.9375rem;
  }
  .content {
    // padding-top:.875rem;
    // padding-left: .625rem;
  }
  .title:before {
    content: " ";
    position: absolute;
    left: 0.125rem;
    top: 50%;
    transform: translateY(-51%);
    width: 0.25rem;
    height: 0.9375rem;
    background: #ff4b4b;
  }
  .foods-item {
    height: 4.625rem;
    padding-top: 0.875rem;
    padding-left: 0.625rem;
    position: relative;
  }
  .food-top {
    height: 1.0625rem;
    // margin-top: .9375rem;
    padding-left: 1.5rem;
    //padding-right: 0.5rem;
    //position: relative;
    .left {
      float: left;
      color: #272727;
      font-size: 1rem;
      line-height: 1.15rem;
    }
    .right {
      float: right;
      color: #ff4b4b;
      font-size: 0.625rem;
    }
  }
  .flag {
    height: 1.2rem;
    display: inline-block;
    padding: 0rem 0.4rem;
    color: #ff4b4b;
    font-size: 0.625rem;
    background: rgba(255, 75, 75, 0.1);
    border-radius: 1rem;
    border: 0.125rem solid #fff;
    vertical-align: middle;
    line-height: 1.2rem;
    // margin-left: .375rem;
  }
  .food-bottom {
    height: 0.8125rem;
    font-size: 0.8125rem;
    color: #929292;
    margin-top: 0.5rem;
    padding-left: 1.5rem;
    padding-right: 0.5rem;
  }
  .foods-item:after {
    @include onepx_top(#e9e9e9);
  }
}
.end {
  text-align: center;
  font-size: 0.6875rem;
  color: #999999;
  padding-top: 0.3125rem;
}
.aa {
  position: relative;
  // width: 4rem;
  padding: 0.3125rem 0.625rem;
  background: #fff;
  z-index: 10;
  color: #7e7e7e;
  font-size: 0.75rem;
  text-align: center;
  img {
    width: 3.125rem;
    height: 3.125rem;
  }
}
.hr-1 {
  display: block;
  position: absolute;
  height: 0.0625rem;
  float: left;
  width: 100%;
  bottom: 0;
  background-color: #d8d8d8;
  -webkit-transform-origin: 0, 0;
  transform-origin: 0, 0;
  -webkit-transform: scaleY(0.5);
  -ms-transform: scaleY(0.5);
  transform: scaleY(0.5);
  // &::after {
  //   @include onepx1(#d8d8d8);
  // }
}
.animation_2::after,
.animation_1::after {
  content: "  ";
  position: absolute;
  left: 0;
  bottom: 0;
  width: 120%;
  height: 0.0625rem;
  background-color: #d8d8d8;
  /* 如果不用 background-color, 使用 border-top:.0625rem solid #f00; 效果是一样的*/
  -webkit-transform: scaleY(0.5);
  transform: scaleY(0.5);
}
.foot_login {
  width: 100%;
  height: 3.1875rem;
  bottom: 0;
  left: 0;
  z-index: 10005;
  color: #fff;
  background: rgba(0,0,0,0.60);
  .login_l {
    float: left;
    padding: 0.5rem 0.625rem 0.5rem 0.9375rem;
    // padding-left: ;

    img {
    width: 2.1875rem;
    height: 2.1875rem;
    }
  }
  .login_m {
    float: left;
    font-family: PingFangSC-Regular;
    font-size: 0.875rem;
    line-height: 3.1875rem;
  }
  .login_r {
    float: right;
    line-height: 3.1875rem;
    span {
      display: inline-block;
      margin: 0.625rem 0.9375rem 0.625rem;
      width: 6.25rem;
      height: 2rem;
      text-align: center;
      font-size: 0.875rem;
      font-family: PingFangSC-Regular;
      line-height: 2rem;
      background-image: linear-gradient(-90deg, #F1238F 0%, #E7175D 100%);
      border-radius: 4px;
    }
  }
}
.fx {
  display: block;
  position: fixed;
}
</style>
