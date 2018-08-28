<template>
  
  <div id="home_header" class="header">
    <ccheader></ccheader>
  </div>

  <div class="cellContainer">
    <!-- <div class="cell_content" v-touch:swipeup="dragUp($event)" v-touch:swipedown="dragDown($event)"> -->
    <div class="cell_content">
      <!-- <div> -->
      <!-- <div class="cellIndex_list item-{{$index}}" v-dragable-for="item in homeList"> -->
      <div class="cellIndex_list item-{{$index}}" v-for="item in homeList">
        <div class="cancelAttention">
          <div class="cancelAttentionDiv" @click="cancelAttentionButton($event,$index,item.indexNo)">
            删除
          </div>
          <div class="stickTop" @click="attentionTopButton($event,$index,item.indexNo,item.topStatus)">
            {{item.topMsg}}
          </div>
        </div>
        
        <div id="touchDiv"></div> 
        <div class="cellList cell-{{$index}}" v-touch:swipeleft="showCancelAttention($event,$index,1)" v-touch:swiperight="hideCancelAttention($event,$index,-1)">
          <div :class="{cellDouble: $index % 2 == 0,cellSingle: $index % 2 == 1}">
          <a @click="onSelected(item.indexName, item.indexNo, 1, item.status, item.identify, item.drillDown,orgHomeList[$index].unit,item.indexType)" href='javascript:void(0);' :class="{active: activePoint == item.indexNo}">
            <div class="cellSubTitle">
              <div class="cellSubTitleImg">
                <img :src="item.icon" />
              </div>      
              <div class="homeCellSubTitleNum_name">{{item.indexName}}</div>
            </div>
            <div class="pointValueArea" v-show="item.runMemo == null || item.runMemo == ''">
              <div class="pointValue">
                <div class="cellPointValue customFont">
                  {{item.value}}
                </div>
              </div>
              <div class="cellUnit">
                <div class="unitDiv">
                  {{item.unit}}
                </div> 
                <!-- 一下是预警图标 warningEarly-->
                <div class="cellSubTitleImg2">
                    <img  v-if="item.warningStatus" :src="warning_red" />
                    <!-- <img v-else :src="" /> -->
                </div>       
              </div>
            </div>
            <div class="pointValueSue" v-show="item.runMemo != null && item.runMemo != ''">
              {{item.runMemo}}
              
            </div>
            
          </a>
          </div>
        </div>
      </div>

      <div class="home_addButton">
        <a @click="toSearch($event)">
          <div class="home_addButtonTouchDiv">
            <img :src="addButton.icon" />
            <div class="fontSize4">{{addButton.title}}</div>
          </div>
        </a>
      </div>

    </div>
  </div>
</template>

<script>

import Vue from 'vue'
import router from 'src/routers';
import Params from 'src/routersParam'
import ParamsData from 'src/js/params_data'
import $ from 'jquery'
import Header from './Common/Header'
import CommonJs from './Common/CommonJs'
import CommonIndicator from './Common/CommonIndicator'
var vueTouch = require('vue-touch')
vueTouch.config = {"swipe":{ direction: 'horizontal' }};
Vue.use(vueTouch)
var currentSwipe;
var count = 0;
var currentClassName;
document.addEventListener('dragstart',function(event){

});

export default {
  name: 'Home',
  data () {
    return {
      homeList: [],
      orgHomeList: [],
      warning_red:require('../assets/images/warning_red.png'),
      warning_yellow:require('../assets/images/warning_yellow.png'),
      addButton: {
        title:'添加关注', icon: require('../assets/images/plus_green.png')
      },

      activePoint: ''
    }
  },
  //计算属性
  computed: {

  },
  watch: {
    'homeList': function () {
    },
  },
  props:{
    data:['0022','0011','0008','0045']
  },
  //在实例开始初始化时同步调用。此时数据观测、事件和 watcher 都尚未初始化
  init() {

  },
  //在实例创建之后同步调用。此时实例已经结束解析选项，这意味着已建立：数据绑定，计算属性，方法，watcher/事件回调。但是还没有开始 DOM 编译，$el 还不存在
  created() {

  },
  //在编译开始前调用
  beforeCompile() {
  },
  //在编译结束后调用。此时所有的指令已生效，因而数据的变化将触发 DOM 更新。但是不担保 $el 已插入文档
  compiled() {
  },
  methods: {
    removeEvent(event) {
        event.preventDefault();
    },
    resetCss() {
      var singleColor = "#455161";
      var doubleColor = "#354254";

      var cellList = $(".cellList");
      
      if (cellList.length % 2 == 0) {
        $(".home_addButton").css('background',singleColor);
      }
      else {
        $(".home_addButton").css('background',doubleColor);
      }

      //获取当前设备屏幕窗口的高宽
      var viewHeight = document.documentElement.clientHeight;
      var viewWidth = document.documentElement.clientWidth;
      //首页cell左右上外边距(用百分比计算以适配不同尺寸web)
      var homeCellMarginLeft = viewWidth * 0.0;
      var homeCellMarginRight = viewWidth * 0.0;
      var homeCellMarginTop = viewWidth * 0.0;
      //首页cell宽高
      var homeCellWidth = viewWidth - homeCellMarginLeft - homeCellMarginRight;
      var homeCellHeight = viewWidth * (7/24);
      //关注高度
      $(".home_addButton").css('height',homeCellHeight);
      $(".cellIndex_list").css('height',homeCellHeight);
      $(".cellSubTitle").css('width',homeCellHeight);
      //图片尺寸
      var cellSubTitleImgWidth = homeCellHeight * (4/12);
      // $(".cellIndex_list img").css('width',cellSubTitleImgWidth);
      $(".cellIndex_list img").css('height',cellSubTitleImgWidth);
      var cellSubTitleImgHeight = homeCellHeight * (5.5/10);
      $(".cellSubTitleImg").css('height',cellSubTitleImgHeight);

      //指标点名称元素高度
      var homeCellSubTitleNum_nameHeight = homeCellHeight * (4.5/10) ;
      //指标点名称字体大小
      var homeCellSubTitleNum_nameFontSize = homeCellSubTitleNum_nameHeight/3.2;

      $(".homeCellSubTitleNum_name").each(function() {
        var titleContent = $(this).text();
        if(titleContent.length >= 14) {
          var homeCellSubTitleNum_nameFontSizeAdjust = homeCellSubTitleNum_nameHeight/3.6;
          $(this).css('font-size',homeCellSubTitleNum_nameFontSizeAdjust);
        }
        else if(titleContent.length >=17) {
          var homeCellSubTitleNum_nameFontSizeAdjust = homeCellSubTitleNum_nameHeight/3.8;
          $(this).css('font-size',homeCellSubTitleNum_nameFontSizeAdjust);
        }
        else {
          $(this).css('font-size',homeCellSubTitleNum_nameFontSize);
        }
      });
      var cellTitleTextCount = parseInt(parseFloat($(".homeCellSubTitleNum_name").css('width'))/parseFloat($(".homeCellSubTitleNum_name").css('font-size')));

      //单位宽度
      var cellUnitWidth = homeCellHeight * 0.5;
      $(".cellUnit").css('width',cellUnitWidth);
      //单位字体大小
      var cellUnitFontSize = cellUnitWidth/4 + 'px';
      $(".unitDiv").css('font-size',cellUnitFontSize);
      //指标点值宽度
      var pointValueWidth = homeCellWidth - homeCellHeight - cellUnitWidth;
      $(".pointValue").css('width',pointValueWidth); 

      
      //平台兼容性处理
      if(ParamsData.method().getMobileSystem() != 1) {
        //指标点值设置行高居中
        var fontOffsetH = 0;
        var valueLineHeight = (homeCellHeight-fontOffsetH)+'px';
        $(".cellPointValue").css('margin-top',fontOffsetH);
        $(".cellPointValue").css('height',valueLineHeight);
        $(".cellPointValue").css('line-height',valueLineHeight);

        $(".cellSubTitleImg2 img").css("height", '15px');
        $(".cellSubTitleImg2 img").css("width", '15px');

        //指标点值得字体大小
        var valueFontSize = homeCellHeight/1.9;
        $(".cellPointValue").css('font-size',valueFontSize);        
      }
      else {
        //指标点值设置行高居中
        var fontOffsetH = 12;
        var valueLineHeight = (homeCellHeight-fontOffsetH)+'px';
        $(".cellPointValue").css('margin-top',fontOffsetH);
        $(".cellPointValue").css('height',valueLineHeight);
        $(".cellPointValue").css('line-height',valueLineHeight);


        $(".cellSubTitleImg2 img").css("height", '15px');
        $(".cellSubTitleImg2 img").css("width", '15px');
        //指标点值得字体大小
        var valueFontSize = homeCellHeight/2;
        $(".cellPointValue").css('font-size',valueFontSize);    
      }

      var cellHeight = $(".cellList").height();
      $(".pointValueSue").css('line-height', cellHeight + "px");    
      $(".pointValueSue").css('font-size',valueFontSize/3);    
    
      //单位
      // $(".unitDiv").css('line-height',valueLineHeight);
      var z = $(".cellIndex_list").css('z-index');
      //取消关注按钮
      $(".cancelAttention").css('height',homeCellHeight);
      $(".cancelAttentionDiv").css('width',homeCellHeight);
      $(".stickTop").css('width',homeCellHeight);
      // $(".cancelAttentionDiv").css('height',homeCellHeight);
      var cancelAttentionDivLineHeight = homeCellHeight+'px';
      $(".cancelAttentionDiv").css('line-height',cancelAttentionDivLineHeight);
      $(".stickTop").css('line-height',cancelAttentionDivLineHeight);
      var cancelAttentionFontSize = homeCellHeight/4.5;
      $(".cancelAttentionDiv").css('font-size',22);
      //重设图片居中
      var imgHeight = $(".home_addButtonTouchDiv img").height();
      var fontHeight = $(".home_addButtonTouchDiv div").height();
      var imgTop = (homeCellHeight - imgHeight - fontHeight)/2;
      $(".home_addButtonTouchDiv img").css('padding-top', imgTop);    
    },
    //请求数据赋值给homeList
    fetchData () {
      var url = ParamsData.method().queryHomePage();
      var homeData = {userNo:window.localStorage.userid,currency:'CN'};
      $(".module_poper_timer").fadeIn();
      var topOrCancelTop = "";

      $.post(url,homeData,(result)=>{
        var homeArray = ParamsData.method().deepClone(result[0].retBody);
        this.orgHomeList = result[0].retBody;
        for (var i = homeArray.length - 1; i >= 0; i--) {
          if (homeArray[i].value == 0) {
            homeArray[i].value = '0.00';
          }
          else {
            homeArray[i].value = parseFloat(homeArray[i].value);
            homeArray[i].value = homeArray[i].value.toFixed(2);
          }
          
          if (homeArray[i].unit == '亿') {
            homeArray[i].unit = ' / ' + homeArray[i].unit;
          }
          else if (homeArray[i].unit == '%') {
            homeArray[i].unit = '　%';
          }
          else if (homeArray[i].unit == '人') {
            homeArray[i].unit = ' / ' + homeArray[i].unit;
            if (homeArray[i].value.length == 0) {
              homeArray[i].value = 0;
            }
            homeArray[i].value = parseInt(homeArray[i].value);
          }
          else if (homeArray[i].unit == '张') {
            homeArray[i].unit = ' / ' + homeArray[i].unit;
            if (homeArray[i].value.length == 0) {
              homeArray[i].value = 0;
            }
            homeArray[i].value = parseInt(homeArray[i].value);
          }
          else if (homeArray[i].unit == '万张') {
            homeArray[i].unit = ' / ' + homeArray[i].unit;
          }
          else if (homeArray[i].unit == '万') {
            homeArray[i].unit = ' / ' + homeArray[i].unit;
          }
          else if (homeArray[i].unit == '万人') {
            homeArray[i].unit = ' / ' + homeArray[i].unit;
          }
          else if (homeArray[i].unit == '户') {
            homeArray[i].unit = ' / ' + homeArray[i].unit;
            if (homeArray[i].value.length == 0) {
              homeArray[i].value = 0;
            }
            homeArray[i].value = parseInt(homeArray[i].value);
          }
          topOrCancelTop = homeArray[i].topStatus == 0?"置顶":"取消置顶";
          homeArray[i].topMsg = topOrCancelTop;
          homeArray[i].warningStatus = this.showWarning(homeArray,i);
          
        }
        this.homeList = homeArray;
        setTimeout(() => {
          this.resetCss(); 
          $(".module_poper_timer").fadeOut();         
        }, 10);
        
        this.getModuleHeaderData();
        
      },'json');
    },
    showWarning:function(homeArray,i){
      var indexValue =  homeArray[i].value;
      var ruleVal =  homeArray[i].ruleVal;
      var warningVal =  homeArray[i].warningVal;
      var warningStatus = false;
      if (typeof(ruleVal) == 'undefined' || typeof(warningVal) == 'undefined') {
        return;
      }
      if (ruleVal == 1) {//表示低于警戒值为不正常
        if (parseFloat(indexValue) <=parseFloat(warningVal)) {
          warningStatus = true;
        }else{
          warningStatus = false;
        }
      }
      else if (ruleVal == 0) {//表示高于警戒值为不正常
        if (parseFloat(indexValue) >= parseFloat(warningVal)) {
          warningStatus = true;
        }else{
          warningStatus = false;
        }
      }
      if (typeof(homeArray[i].runMemo) != 'undefined') {
          warningStatus = false;
      }
      return warningStatus;
    },
    getModuleHeaderData: function(){
 
    },
    onSelected: function(name,indexNo,attentionState,status,identify,drillDown,unit,indexType) {
      // router.go({name:'manager'});
      // return;
      this.activePoint = indexNo;
      //判断如果下钻字段为空或为空字符串赋值0
      if (drillDown == null || drillDown == '') {
        drillDown = 0;
      };
      //点击跳转前判断当前是否有左滑显示置顶删除按钮有则先left:0
      if (count != 0) {
        $(currentClassName).animate({left:0});
        count = 0;
      }
      else {
        var backName = '首页';
        var firstBackName = '';
        var authorityNo = 0;
        var hiddenModuleSwitch = false;
        /*
        机构级别
         */
        var orgLevel = 0;
        //用户登录接口返回当前登录用户所在机构号，根据机构号查询详情
        var orgNo = window.localStorage.orgNo;
        var indexUnit;
          indexUnit = unit;

        router.go({name:'index_assets',
          params:{
            pointName:name,   //指标名称
            pointNo:indexNo,    //指标编码
            attentionState:attentionState,    //关注状态
            status:status,
            orgNo:orgNo,    //机构编号
            backName:backName,     //导航返回标题名
            firstBackName: firstBackName,
            // authorityNo:authorityNo,   //权限编码
            hiddenModuleSwitch: hiddenModuleSwitch,
            orgLevel: orgLevel,
            identify: identify,
            drillDown: drillDown,
            indexType:indexType,
            unit: indexUnit,
            cascFlag: 0
          }});
      }
    },
    dragUp: function(event) {
    },
    dragDown: function(event) {
    },
    //左滑显示取消关注按钮
    showCancelAttention: function(event,index) {
      event.preventDefault=true; 
      count = count + 1;
      if (count == 1) {
        currentClassName = '.cell-'+index;
        var w = $(currentClassName).css("width");
        currentSwipe = index;
        //向左滑动显示取消关注
        var toLeft = $(".cellSubTitle").css('width');
        toLeft = '-'+parseFloat(toLeft)*2+'px';
        $(currentClassName).animate({left:toLeft});
      }
      else if (count > 1) {
        if (currentSwipe == index) {
          //滑动恢复正常
          $(currentClassName).animate({left:0});
          count = 0;
        }
        else {
          $(currentClassName).animate({left:0});
          count = 0;
        }
      }
    },
    //右滑隐藏取消关注按钮
    hideCancelAttention: function() {
      $(currentClassName).animate({left:0});
      count = 0;
    },
    //点击取消关注
    cancelAttentionButton: function(event,index,indexNo) {
      //发送请求取消关注closeAttentionUrl
      var url = ParamsData.method().getCloseAttentionUrl();
      var data = {userNo:window.localStorage.userid,indexNos:indexNo};    //key/value参数
      $.post(url,data,(result)=>{
        $("#prompt").text('取消关注成功')
        $("#prompt").fadeIn();
        setTimeout(()=>{
           $("#prompt").fadeOut();
        },1000)
        this.fetchData();
        $(".cellContent").hide();
        setTimeout(() => {
              $(".cellContent").show();
              this.resetCss();    

          }, 0)
        count = 0;
      },'json');
    },
    //置顶或着取消置顶
    attentionTopButton: function(event,index,indexNo,topStatus) {
      var url = ParamsData.method().getStickTop();
      // var params = {userNo:window.localStorage.userid,indexNo:indexNo,top:'T',currency:'CN'};
      var params = topStatus == 0?{userNo:window.localStorage.userid,indexNo:indexNo,top:'T',currency:'CN'}:{userNo:window.localStorage.userid,indexNo:indexNo,top:'0',currency:'CN'};
      $.post(url, params, (result)=> {
        this.fetchData();
        $(".cellContent").hide();
        setTimeout(() => {
              $(".cellContent").show();
              this.resetCss();    

          }, 0)
        count = 0;
      }, 'json');
    },
    toSearch: function($event) {
      var limits = window.localStorage.limits;
      //如果为客户经理权限。则直接跳转至搜索页面。不跳转指标分类页面／
      if (limits == 6) {
        router.go({ name: 'search',params:{index_type:'4'}});
      }else{
        router.go({ name: 'index_column'});
      }
    },
  },
  attached () {
  },
  detached() {
  },
  beforeDestroy() {
  },
  destroyed() {

  },
  ready() {
    this.fetchData();
    var state = ParamsData.method().getAttentionState();
    if (state == 1) {
      $("#prompt").text('关注成功')
      $("#prompt").fadeIn();
      setTimeout(()=>{
        $("#prompt").fadeOut();
      },1000)
      ParamsData.method().setAttentionState(0);
    }
    else{

    }
  
    document.body.removeEventListener('touchmove', this.removeEvent);
    $(".cctitle").text(ParamsData.method().getAppTitle());

  }
}

//左->右滑动显示
$.fn.slideLeftToRightShow = function(speed, callback) {
  this.animate({
    width: "show",
    paddingRight: "show",
    paddingLeft: "show",
    marginRight: "show",
    marginLeft: "show",
    left: 0
  }, speed, callback);
}

//右->左滑动隐藏
$.fn.slideRightToLeftHide = function(speed, callback) {
  this.animate({
    width: "hide",
    paddingLeft: "hide",
    paddingRight: "hide",
    marginLeft: "hide",
    marginRight: "hide",
    left: 0
  }, speed, callback);
}

//左->右滑动隐藏
$.fn.slideLeftToRightHide = function(speed, callback) {
  this.animate({
    width: "hide",
    paddingRight: "hide",
    paddingLeft: "hide",
    marginRight: "hide",
    marginLeft: "hide",
    right: 0
  }, speed, callback);
}

//右->左滑动显示
$.fn.slideRightToLeftShow = function(speed, callback) {
  this.animate({
    width: "show",
    paddingLeft: "show",
    paddingRight: "show",
    marginLeft: "show",
    marginRight: "show",
    right: 0
  }, speed, callback);
}

//下->上滑动隐藏
$.fn.slideDownToUpHide = function(speed, callback) {
  this.animate({
    'margin-top': -moduleHeight
  }, speed, callback);
}

//上->下滑动显示
$.fn.slideUpToDownShow = function(speed, callback) {
  this.animate({
    'margin-top': moduleHeight
  }, speed, callback);
}
</script>

<style type="text/css">
.active {
  background: #ff0000;
}

.cellDouble {
  height: 100%;
  background: #455161;
}

.cellSingle {
  height: 100%;
  background: #354254;
}
.cellSubTitleImg2{
  width: 30px;
  height: 30px;
  margin-top: -60px;
  text-align: right;
  vertical-align: middle;
  float: right;
  position: absolute;
}


.cellSubTitleImg2 img{
  position: absolute;
}
</style>
