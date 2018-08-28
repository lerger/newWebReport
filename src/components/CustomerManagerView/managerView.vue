<template>
	<div id="search_header" class="header">
        <ccsearchheader></ccsearchheader>
    </div>
    <div class="searchBoxDiv">
        <input id="orgSearchBox" class="searchBox" placeholder="" v-model="items" @focus="touchSearch" @blur="touchSearchOut">
        <div @click="touchSearch" class="searchSuePlace"><img src="../../assets/images/searcher.png" /><span class="searchTitle"> 请输入工号或姓名进行搜索</span></div>
    </div>
    <div class="cancelBtn" @click="cancelBtnClick($event)"><img src="../../assets/images/searcher.png" /></div>
    <div class="searchLayer">
	    <div class="searchResultList">
	         <div class="resultPrompt">没有搜索到客户经理</div>
	         <searchresult :params="searchParams"></searchresult>
	    </div>
    </div>
	<ol class="tree" id="treeId"></ol>
</template>
<script type="text/javascript">
import $ from 'jquery'
import Vue from 'vue'
import router from 'src/routers'
import ParamsData from 'src/js/params_data'
import SearchHeader from '../Common/SearchHeader'
import SearchResult from '../SearchResult'
var self;
var openImg = require('../../assets/images/open.png');
var closeUpImg = require('../../assets/images/close_up.png');
var isFirst = true;
var targetList = [];
var items;
var searchParams = [];
export default {
    data() {
        return {
            targetList:targetList,
            items:items,
            searchParams:searchParams,
            userList:[]
        }
    },
    watch:{
        targetList:{
            handler:function(val,oldval){   
                $(".tree label").off("click");
                $(".tree label").click(function(){
	                //根据id筛选出这个标签的信息对象
	                var clickItem = {};
	                for (var i = 0; i < self.userList.length; i++) 
	                {
	                	if (self.userList[i].branchInnerCode == this.id) 
	                	{
	                        clickItem = self.userList[i];
	                	}
	                };
	                //如果为用户就直接跳转至指标详情界面。否则直接请求下一节点的数据。
	                if (clickItem.isUser == 'YES') {
	                    var pointNo = self.$route.params.pointNo;
			            var pointName = self.$route.params.pointName;
			            var status = self.$route.params.status;
			            var orgNo = self.$route.params.orgNo;
			            var backName = '返回';
			            var unit = self.$route.params.unit;
			            router.go({name:'index_assets',params:{
				            userNo:clickItem.username,
				            pointNo:pointNo,
				            status:status,
				            orgNo:clickItem.branchInnerCode,
				            backName:backName,
				            unit:unit,
				            isFromeOrgListJump:'YES',
			            }});
	               	    return;
	                }
	                var isChecked = $(this).next("input[type='checkbox']").is(':checked');
	                //展开和收齐的不同状态下更换右侧小图标
	                var imgId = "img"+this.id;
	                if (isChecked) 
	                {
	                    $("#"+this.id).css('background-image','url('+openImg+')')
	                }
	                else
	                {
	                    $("#"+this.id).css('background-image','url('+closeUpImg+')')
	                    requestOrgListData(this.id);
	                }
		                
                })
           },
           items:{
	           handler:function(val,oldval){
	            
	            
	           },
           },
           deep:true
        }
    }, 
    methods: {
        cancelBtnClick:function(enve){
            var serchStr = $("#orgSearchBox")[0].value;
            testRequest(serchStr);
            $(".rightbutton").fadeIn();
        },
        touchSearch: function() {
        	// $(".searchLayer").fadeIn();
            $(".cancelBtn").fadeIn();
            $(".searchSuePlace").fadeOut(200);
            $("#orgSearchBox").focus();

        },
        touchSearchOut: function() {
            if ($("#orgSearchBox")[0].value == "") {
                $(".searchSuePlace").fadeIn(200);
                $(".searchLayer").fadeOut();
                $(".cancelBtn").fadeOut();
                // $(".initials").fadeIn();
                // $(".parent").css('position','relative');
                $(".rightbutton").fadeIn();
            }
        }
    },   
    ready(){
        self = this;
        isFirst = true;
        $(".rightbutton").css('font-size','22px');
	    $(".rightbutton").text('');
	    $(".rightbutton").hide();
	    $(".ccheader").css('background-color','#3b4757');
	    $(".cctitle").css('background-color','#3b4757');  
        requestOrgListData();
    }
}


function testRequest(serchStr){
    var customerViewOrgSerchByStrListUrl = ParamsData.method().customerViewOrgSerchByStrListUrl();
    // var name = encodeURI("龙翔");
	var testParams = {userNo:window.localStorage.userid,str:serchStr};
	$.post(customerViewOrgSerchByStrListUrl,testParams,(result)=> {
		var managerList =  result[0].retBody.user;
		var tmpUserList = [];
		if (typeof(managerList) != 'undefined') {
			$(".resultPrompt").fadeOut();
			for (var i = 0; i < managerList.length; i++) {
	        	var manageObj = {indexNo:managerList[i].username,indexName:managerList[i].realname+'_'+ managerList[i].username,branchInnerCode:managerList[i].username,isUser:'YES'};
	        	tmpUserList.push(manageObj);
            };
		}else{
			$(".resultPrompt").fadeIn();
		}
        $(".tree").css('position','fixed');
        self.searchParams =  tmpUserList;
		$(".searchLayer").fadeIn();
        $(".cancelBtn").fadeIn();
	}, 'json');
}

function requestOrgListData(branch_innerCode){
	var customerViewOrgFinderListUrl = ParamsData.method().customerViewOrgFinderListUrl();
    var indexNo = self.$route.params.pointNo;
    var orgFinderListParams = {userNo:window.localStorage.userid,branchInnerCode:'',indexNo:indexNo};
    if (branch_innerCode && typeof(branch_innerCode) != 'undefined' && branch_innerCode.length != 0) 
    {
    	isFirst = false;
        orgFinderListParams = {userNo:window.localStorage.userid,branchInnerCode:branch_innerCode,indexNo:indexNo};
    }
    $(".module_poper_timer").fadeIn();
    $.post(customerViewOrgFinderListUrl,orgFinderListParams,(result)=> {
        $(".module_poper_timer").fadeOut();
        var orgArray = [];
        if(result[0].retState == true && result[0].retBody.branch) 
        {
            orgArray = result[0].retBody.branch;
        }
        var userArrary = [];
	    if(result[0].retState == true && result[0].retBody.user) 
	    {
	        var users = result[0].retBody.user;
	        for (var i = 0; i < users.length; i++) 
	        {
	            var newUserObj = {name:users[i].realname + '_' + users[i].username,branchInnerCode:users[i].username,username:users[i].username,isUser:'YES'};
	            userArrary.push(newUserObj);
	        }
	        self.userList = self.userList.concat(userArrary);
	    }
	    orgArray = orgArray.concat(userArrary);
	    self.targetList = orgArray;
	    if (isFirst) 
	    {
        	document.getElementById("treeId").innerHTML = crentDivStr(self.targetList);
        }
        else
        {
        	document.getElementById("stree"+branch_innerCode).innerHTML = crentDivStr(self.targetList);
        }


    }, 'json');
}

function crentDivStr(targetList){
	let treeLi = '';
	for (var i = 0; i < targetList.length; i++) {
        var  labelId =  targetList[i].branchInnerCode;
        var  folder = "folder" + targetList[i].branchInnerCode;
        var  streeId = "stree" + targetList[i].branchInnerCode;
        var  imgId = "img" + targetList[i].branchInnerCode;
	    let childLi =`<li><label for=${folder} class="folderOne" id=${labelId} @click="toBranchOrgDetail($event,${targetList[i].name})"><img class="imgNumClass" id=${imgId} /><span>${targetList[i].name}</span></label> <input type="checkbox" id=${folder} />
	                <ol id=${streeId}></ol></li>`
     treeLi += childLi;
	};
	return treeLi;
}
</script>
<style type="text/css">
.tree {margin: 30px 0px 0px 0px;padding: 0;/*background-color:#8a8a000*/;overflow: hidden;}  
    /*隐藏input*/
.tree li input{position: absolute;left: 0;opacity: 0;z-index: 2;cursor: pointer;height: 1em;width:1em;top: 0;}  
/*所有菜单项设置统一样式*/
.tree li {position: relative;list-style: none;}   
/*一级菜单加下边线*/
.tree>li{/*border-bottom: 1px solid #d9d9d9;*/}
/*给有子菜单的菜单项添加背景图标*/
.tree li label {max-width:999px;cursor: pointer;display: block;margin:0 0 0 -50px;padding: 15px 10px 15px 70px;background: url(../../assets/images/open.png) no-repeat right center;background-position:12.5% 50%;white-space:nowrap;overflow:hidden;text-overflow: ellipsis; background-size: 15px 15px;border-bottom: 0.5px solid #737d89;}  
.tree li label:hover,li label:focus{/*background-color:#020202*//*;color:#020202;*/}
/*清除所有展开的子菜单的display*/
.tree li input + ol{display: none;}  
/*当input被选中时，给所有展开的子菜单设置样式*/
.tree input:checked + ol {padding-left:14px;height: auto;display: block;}  
.tree input:checked + ol > li { height: auto;}  
/*末层菜单为A标签，设置样式*/
.tree li.file a{margin:0 -10px 0 -50px;padding: 15px 20px 15px 70px;text-decoration:none;display: block;color:#333333;white-space:nowrap;overflow:hidden;text-overflow: ellipsis;} 
.tree li.file a:hover,li.file a:focus{background-color:#a7a7a7;color:#999999;} 
/*不同层级的菜单字体大小不同*/
.tree .folderOne{font-size: 18px;}
.tree .folderTwo{font-size:16px;}
.tree .folderThree{font-size:14px;}
.imgNumClass{width: 0px;height: 0px;}



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
.searchSuePlace {
    position: absolute;
    top: 0px;
    margin:1px 0%;
    width: 100%;
    height: 26px;

    text-align: center;
}
.searchSuePlace img {
    width: 16px;
    height: 16px;
    vertical-align: -2px;
}

.searchSuePlace .searchTitle {
    /*float: left;*/
    /*width: 47%;*/
     
    /*text-align: left;*/
    /*padding-left: 3%;*/
    color: #F8F8FF;
    font-size: 15px;
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
</style>