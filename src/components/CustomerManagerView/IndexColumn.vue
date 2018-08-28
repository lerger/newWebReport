<template>
  <div id="search_header" class="header">
    <ccsearchheader></ccsearchheader>
  </div>
  <div class="searchBoxDiv">
    <input id="columm_searchBox" class="searchBox" placeholder="" v-model="items" @focus="touchSearch" @blur="touchSearchOut">
    <div @click="touchSearch" class="searchSue"><div class="searchImg"><img src="../../assets/images/searcher.png" /></div><div class="searchTitle">搜索</div></div>
  </div>
  <div class="cancelBtn" @click="cancelBtnClick($event)"><img src="../../assets/images/delete.png" /></div>
  <div class="searchLayer">
      <div class="searchResultList">
         <div class="resultPrompt">没有搜索到相关指标</div>
         <searchresult :params="searchParams"></searchresult>
      </div>
  </div>
  <div class="parent">
    <div class="classifiedContentBg">
      <div class="classifiedContent">
        <div class="column manage" @click="indexClassificationClick($event,'1')">
          <img src='../../assets/images/supervise.png'>
          <span>监管</span>
        </div>
        <div class="column supervise" @click="indexClassificationClick($event,'2')">
          <img src='../../assets/images/manage.png'>
          <span>经营</span>
        </div>
          <div class="column assess" @click="indexClassificationClick($event,'3')">
            <img src='../../assets/images/assess.png'>
            <span>考核</span>
          </div>
          <div class="column personage" @click="indexClassificationClick($event,'4')">
            <img src='../../assets/images/personge.png'>
            <span>个人业绩</span>
          </div>
      </div>
    </div>
  </div>
</template>
<script type="text/javascript">
import Vue from 'vue'
import router from 'src/routers';
import Params from 'src/routersParam'
import ParamsData from 'src/js/params_data'
import $ from 'jquery'
import searchJs from '../../components/searchJs'
import SearchHeader from '../Common/SearchHeader'
import SearchResult from '../SearchResult'
var searchParams = [];
var allIndextList = [];
export default {
    data() {
        return {
            searchParams:searchParams,
            items:searchParams
        }
    },
    watch:{
        items:{
           handler:function(val,oldval){
            var  searchParamsTmp = filterTarget(this.items);
   
            var resultPrompt = $(".resultPrompt");
            if (searchParamsTmp.length == 0) {
                resultPrompt.fadeIn();
            }
            else{
                resultPrompt.fadeOut();
            }
            this.searchParams = searchParamsTmp;

            $(".searchLayer").fadeIn();
            $(".cancelBtn").fadeIn();
            // $(".parent").css('position','fixed');

            // $(".initials").fadeOut();
            $(".rightbutton").fadeOut();
            
           },
           deep:true
        }
    },
    methods: {
        indexClassificationClick:function(event,index_type){
            var limits = window.localStorage.limits;
            if (limits == 6 ) {
                if (index_type == 1 || index_type == 2 || index_type == 3) {
                    alert('您没有权限查看此类指标');
                    return;
                }
            }
            router.go({ name: 'search',params:{index_type:index_type}});
        },
        resetCss() {
          var viewHeight = document.documentElement.clientHeight;
          var viewWidth = document.documentElement.clientWidth;
          var columnImgWidth = viewWidth*(1/3);
          $(".column>img").css('width',columnImgWidth);
          $(".column>img").css('height',columnImgWidth +10);
          var subTitleNum_nameFontSizeAdjust  = viewWidth * (7/24)* (4.5/10)/2.2;
          $(".column>span").css('font-size',subTitleNum_nameFontSizeAdjust)
        },
        cancelBtnClick:function(enve){
            $(".searchSue").fadeIn(200);
            $(".searchLayer").fadeOut();
            $(".cancelBtn").fadeOut();

            // $(".initials").fadeIn();
            // $(".parent").css('position','relative');
            $("#columm_searchBox")[0].value = null;
            $(".rightbutton").fadeIn();
        },
        touchSearch: function() {
            $(".searchSue").fadeOut(200);
            $("#columm_searchBox").focus();

        },
        touchSearchOut: function() {
            if ($("#columm_searchBox")[0].value == "") {
                $(".searchSue").fadeIn(200);
                $(".searchLayer").fadeOut();
                $(".cancelBtn").fadeOut();
                // $(".initials").fadeIn();
                // $(".parent").css('position','relative');
                $(".rightbutton").fadeIn();
            }
        }
    },
    ready() { 

        if(window.navigator.onLine==true){
            var _this = this;
        }
        else{
         }
        this.resetCss();
        scrollTo(0,0);
       
        $(".searchBox").focus(function(){
     
         });
        requestAllIndext();
        $(".cctitle").text(ParamsData.method().getAppTitle());
        $(".rightbutton").css('font-size','22px');
        $(".rightbutton").text('');
        $(".rightbutton").hide();
        $(".ccheader").css('background-color','#3b4757');
        $(".cctitle").css('background-color','#3b4757');      
    }
}

function requestAllIndext(){
    var attentionUrl = ParamsData.method().getSearchIndexList();
    var params = {userNo:window.localStorage.userid,currency:'CN'};
    $(".module_poper_timer").fadeIn();
    $.post(attentionUrl, params, function(result){
        var attentionArray = result[0].retBody;
        $(".module_poper_timer").fadeOut();
        allIndextList = attentionArray;
    },'json');
}

function filterTarget(value){
  var serchResultList = [];
  for (var i = 0; i < allIndextList.length; i++) {
      if (allIndextList[i].indexName.indexOf(value) !=-1 && value != '') {
         serchResultList.push(allIndextList[i]);
      }
  };
  return serchResultList;
}

</script>
<style type="text/css"> 
html,body { 
width:100%; 
height:100%; 
margin:0px; 
padding:0px; 
/*overflow:hidden; */
} 
ul,li,ol,input,textarea,p,dl,dt,dd{
    margin:0;
    padding:0;
    font-family: "Microsoft YaHei, Arial, sans-serif";
   
}
a,input,img,textarea,span,div{
    outline: 0;
    -webkit-tap-highlight-color:rgba(255,0,0,0);
}
input::-webkit-input-placeholder { /* WebKit browsers */
    color:white;
    font-size:16px;
}
input:-ms-input-placeholder { /* Internet Explorer 10+ */
    color:white;
    font-size:16px;
}
:-moz-placeholder { /* Mozilla Firefox 4 to 18 */
    color:white;
    font-size:16px;
}
::-moz-placeholder { /* Mozilla Firefox 19+ */
    color:white;
    font-size:16px;
}
.searchBoxDiv {
/*    position: fixed;*/
    position: absolute;
    top: 64px;
    left: 0;    
    margin:0px 2% 0px 2%;
    width: 96%;
    height: 30px;
    line-height: 30px;
    border-radius: 15px;
    font-size: 16px;
    background-color: #56606e;
    text-align: center;
    color: #ffffff;
    outline: none;
    z-index: 10;
}
.searchBox {
    position: relative;
    font-size: 16px;
    width: 94%;
    height: 30px;
    line-height: 30px;
    padding: 0 3%;
    border: 0 solid white;
    background-color: transparent;
    color: white;
    /*display: none;*/
    /*outline-color:red;*/
}
.searchSue {
    position: absolute;
    top: 0px;
    margin:1px 25%;
    width: 50%;
    height: 25px;
    text-align: center;
}
.searchSue img {
    width: 20px;
    height: 20px;
    vertical-align: -4px;
}
.searchSue .searchImg {
    float: left;
    width: 47%;
    text-align: right;
    padding-right: 3%;
}
.searchSue .searchTitle {
    float: left;
    width: 47%;
     
    text-align: left;
    padding-left: 3%;
    font-size: 20px;
}
.searchLayer{
    width: 100%;
    height: 100%;
    line-height: 50px;
    position: fixed          ;
    display: none;
    /*left: 20px;*/
    background-color: #3b4757;
    right: 0px;
    margin-top: 45px;
    z-index: 110;
    overflow: auto;
}
.searchResultList{
    position: absolute;
    width: 100%;
    height: 100%;
    line-height: 50px;
    z-index: 112;
}

.searchresult{
    margin-top: 0px;
}
.parent{position:absolute;width:100%;top: 94px;/*height:100%;*/background-size:100% 100%;/*margin-top:0px;*/bottom: 0px;/*top:0px*/;background-image: url(../../assets/images/fenlei_bg.png)}
.classifiedContentBg{
  position: absolute;
  width:100%;
  top: 0px;
  bottom:0px;
}
.classifiedContent{position: absolute;margin: auto;width: 100%;height: 450px;top: 0;left: 0;right: 0;bottom: 0;}
.column{/*margin-top:40px;*/margin-bottom:40px;/*float:left;*//*width:180px;height:150px;*//*margin-left:50px;*//*margin-top:40px*/;/*box-sizing:border-box;*//*background-color:#EE00EE;*/ /*border-radius:10px*/;
 }

 .column img{
    width: 140px;/*140px*/
    height: 150px;/*150px*/
 }
.column span{
    height:30px; line-height:30px; display:block; color:#FFF; text-align:center;font-size: 24px;
}
.manage{
    float: left;
    margin-left: 10%;
}
.supervise{
    float: right;
    margin-right: 10%;
}
.assess{
   float: left;
   margin-left: 10%;
   
}
.personage{
   float: right;
   margin-right: 10%;
}
.cancelBtn {
    position: absolute;
    z-index: 10;
    right: 12px;
    color: #ffffff;
    width: 26px;
    height: 26px;
    display: none;
}
.cancelBtn img {
    width: 16px;
    height: 16px;
    margin-top: 7px;
}
.resultPrompt{
    position: fixed;
    width: 100%;
    height: 50px;
    display: none;
    text-align: center;
    margin-top: 50%;
    font-size: 22px;
}
.result_list{
    margin:0px 4.83% 0px 4.83%;
    padding:0px 0px 0px 0px;
    position: relative;
    height: 49px;
    line-height: 50px;
    color: #ffffff;
    border-bottom:0.5px solid #737d89;
}
.num_logo{
    width: 11px;
    height: 50px;
    line-height: 48px;
    position: absolute;
    padding-top: 1.25%;
    right: 5px;
    display: block;
}
.num_logo img{
    width: 11px;
    height: 20px;
    /*line-height: 50px;*/
}
</style>