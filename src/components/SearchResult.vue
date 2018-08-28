<script>
import Vue from 'vue'
import $ from 'jquery'
import router from '../routers';
var searchresult = Vue.extend({
  template: `
    <div class="searchresult">
      <div class="searchresultContainer">
        <div  v-for="listItem in params">
          <div class="result_list listItem-{{$index}}"  @click="onSelected(listItem)">
              <div class="num_logo">
                <img :src="rightLogo"/>
              </div>
              <div class="num_name" id="resultName-{{listItem.indexNo}}">{{listItem.indexName}}</div>
          </div>
        </div> 
      </div>
    </div>
  `,
  data: function() {
    return { 
         rightLogo: require('../assets/images/arrow_right.png'),

    }
  },
  props: [
    'params'
  ],

  methods: {
    onSelected:function(listItem){
      var limits = window.localStorage.limits;
      if (listItem.indexType == '4' && limits != '6') {//如果为客户经理指标则跳转至机构页面
        var backName = '返回';
        var orgNo = window.localStorage.orgNo;
        router.go({name:'manager',params:{
              pointNo:listItem.indexNo,
              branch_innerCode:'undefined',
              pointName:listItem.indexName,
              status:listItem.status,
              orgNo:orgNo,
              backName:backName,
              unit:listItem.unit,
          }});
        return;
      }
      //如果为用户就直接跳转至指标详情界面。否则直接请求下一节点的数据。
      if (listItem.isUser == 'YES') {
        console.log('已经是用户啦');
        var pointNo = this.$route.params.pointNo;
        var pointName = this.$route.params.pointName;
        var status = this.$route.params.status;
        var orgNo = this.$route.params.orgNo;
        var backName = '返回';
        var unit = this.$route.params.unit;
        router.go({name:'index_assets',params:{
          userNo:listItem.branchInnerCode,
          pointNo:pointNo,
          status:status,
          orgNo:listItem.branchInnerCode,
          backName:backName,
          unit:unit,
          isFromeOrgListJump:'YES',
        }});
          return;
      }
      var orgNo = window.localStorage.orgNo;
      router.go({name:'index_assets', params:{pointName:listItem.indexName,pointNo:listItem.indexNo,attentionState:listItem.attentionState,orgNo:orgNo,backName:'搜索'}});
    }
  },

});

Vue.component('searchresult', searchresult);

export default {
  data () {
    return {
      
    }
  },
  ready: {
    
    
  },

  methods: {
   
 
  },

  
}
</script>

<style type="text/css">
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
    white-space:nowrap;
    overflow:hidden;
    text-overflow: ellipsis;
    border-bottom:0.5px solid #737d89;
}

.num_name{
  width: 95%;
  white-space:nowrap;
  overflow:hidden;
  text-overflow: ellipsis;
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
.searchLayer{
    width: 100%;
    height: 100%;
    line-height: 50px;
    position: fixed;
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
</style>