<script>
import Vue from 'vue'
import $ from 'jquery'
import ParamsData from 'src/js/params_data'
import router from 'src/routers';
var header = Vue.extend({
  template: `
    <div class="ccindexheader">
      <div class="ccindexheaderContainer">
        <div class="backbutton">
          <a v-link="{ name: backButton.link, params:{userId:1} }">
            <div>
              <img :src="backButton.icon" />
              <span style="font-family: 'Arial';">{{backButton.title}}</span>
            </div>
          </a>
        </div>
        <div class="cctitle ccindexheaderbg">App Title</div>
        <div class="indexrightbutton" @click = "indexrightbuttonclick()">

            <div>
              <img v-if="isNewPoint" :src="redIcon" class="flash"/>
              <img v-else :src="rightButton.icon" />
              <span>{{rightButton.title}}</span>
            </div>
         
        </div>
      </div>
    </div>
  `,
  data: function() {
    return { 
      backButton: {title:'OA', icon: require('../../assets/images/back.png'), link: 'exit'},
      rightButton: {title:'', icon: require('../../assets/images/column.png'), link: 'index_column'},
      isNewPoint: false,
      redIcon: require('../../assets/images/column_new.png'),
    }
  },
  methods: {

    indexrightbuttonclick:function(){
      var limits = window.localStorage.limits;
      //如果为客户经理权限。则直接跳转至搜索页面。不跳转指标分类页面／
      if (limits == 6) {
        router.go({ name: 'search',params:{index_type:'4'}});
      }else{
        router.go({ name: 'index_column'});
      }
    },
    getModuleHeaderData: function(){
        //获取客户端设备DOM宽高
        var width = document.documentElement.clientWidth;
        var height = document.documentElement.clientHeight;
        
        var data = ParamsData.method().getModuleHeaderParams();

        var currentDate = new Date();
        var year = currentDate.getFullYear();
        var month = currentDate.getMonth();
        var day = currentDate.getDate();
        var date = ParamsData.method().generateFormatDate(year,month,day);
        date = parseInt(date);
        if(data.length == 0) {
          var url = ParamsData.method().getSearchIndexList();
          var params = {userNo:window.localStorage.userid};
          $.post(url,params,(result)=>{
            var pointArray = result[0].retBody;  
            var allPointNameArray = [];
            //当有新指标时首页右上角消息图标显示红色，无新指标时显示白色            
            for (var i = pointArray.length - 1; i >= 0; i--) {
               allPointNameArray.push(pointArray[i]);
               if (pointArray[i].topEndDate.length > 0) {
                  if (date <= parseInt(pointArray[i].topEndDate)) {
                      this.isNewPoint = true;
                   };
               }
               // else {
               //  this.isNewPoint = false;
               // }
            }
            allPointNameArray.reverse();
            ParamsData.method().setModuleHeaderParams(allPointNameArray);
          }, 'json');
        } 

        for (var i = data.length - 1; i >= 0; i--) {
          if (date <= parseInt(data[i].topEndDate)) {
            this.isNewPoint = true;
          }
        }
    },
  },
  created() {
    
  },
  ready () {
    $("body").css('position', 'relative');
    setTimeout(function(){
        $('.flash').addClass('flash');
    }, 1000);
    this.getModuleHeaderData();

  }
});

Vue.component('ccheader', header);

export default {
  data () {
    return {
      
    }
  }
}
</script>
