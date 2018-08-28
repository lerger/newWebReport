<script type="text/javascript">
import Vue from 'vue'
import $ from 'jquery'
import Echart from 'echarts'


var pieChartContainer;
var myPieChart;
var pieColorArray = ['#ffd687','#87ffb7','#87b7ff','#9287ff','#ff8787','#e4b6ee','#fbc566','#b9fa97','#DA70D6'];
// var serisValArray = [1];
var name = '???';
var data;
var title;

var pieOption = {
	title: {
		text: title,
		textStyle: {
			color: '#ffffff',
			fontSizeL: 20,
		},
		textAlign: 'center',
		textBaseline: 'middle',
		// x: 'middle',
		// y: 'middle',
		left: '49%',
		right: '50%',
		top: '49%',
		bottom: '50%' 
	},
	color: pieColorArray,      //
    avoidLabelOverlap: false,
    tooltip : {
      show: true,     //true触发回调方法,false不触发
      //backgroundColor: 'rgba(119,119,202,0.5)',
      showContent: true,
      trigger: 'axis',   
      triggerOn: 'click',
      alwaysShowContent: true,
      position: ['50%','0%'],
      // position: function(point,params,dom) {

      // },
      formatter: function (params, ticket, callback) {
        
      },
      backgroundColor: "#010102",
      axisPointer : {            // 坐标轴指示器，坐标轴触发有效
          type : 'line'          // 默认为直线，可选为：'line' | 'shadow'
      }
    },
    series: [
    {
      name: '资产',
      type: 'pie',
      legendHoverLink: false,     //是否启用图例 hover 时的联动高亮
      hoverAnimation: false,      //是否开启 hover 在扇区上的放大动画效果
      clockwise: true,            //饼图的扇区是否是顺时针排布
      startAngle: 45,
      //smooth: true,
      //stack: '负债总额',
      //barWidth: '50%',
      radius: ['48%', '80%'],
      label: {
        normal: {
            show: true,
            position: 'center',
            formatter: function(params) {
            	
            },
            textStyle: {
              color: 'white',
              fontSize: 16,
            }
        },
      },
      data: data
    },     

    ],
    animation: true,      //是否开启动画效果    
    animationThreshold : 2000,
    animationDuration: 500,    //初始动画的时长
};

/* 初始化环形图

*/
function init(initPieData,initTitle) {
	title = formatTitleString(initTitle);
	data = initPieData;
	pieChartContainer = document.getElementById('pieChartId');

	myPieChart = Echart.init(pieChartContainer);

	pieOption.title.text = title;
	pieOption.series[0].data = initPieData;

	myPieChart.setOption(pieOption);
	// myPieChart.setOption(pieOption.series[0].data);
}

//动态更改环形图数据
function setData(seriesValue) {
	var data = pieOption.series[0].data;
	for (var i = 0; i < data.length; i++) {
		pieOption.series[0].data[i].value = seriesValue[i];
	}
	myPieChart.setOption(pieOption);
}

//标题文本换行处理
function formatTitleString(name) {
	var subName = '';
	if (name.length > 10) {
  		var count = parseInt(name.length / 5);
  		var mod = name.length%5;
  		for (var i = 0; i < count; i++) {
  			var sumName;
  			sumName = name.substring(5*i,5*(i+1)) + '\n';
  			subName = subName + sumName;
  		}
  		subName = subName + name.substring(5*count,5*count+mod);
  	}
  	else if (name.length == 10) {
  		subName = name.substring(0,5) + '\n' + name.substring(5,10);
  	}
  	else if (name.length > 5) {
  		subName = name.substring(0,4) + '\n' + name.substring(4,name.length);
  	}
  	else {
  		subName = name;
  	}
  	return subName;
}

export default{
	data() {
		return {

		}
	},
	method() {
		return {
			init: init,
			setData: setData
		}
	}
}	
</script>

<style type="text/css">
.pieChartClass {
	width: 100%;
	height: 250px;
	border-bottom:0.5px solid #737c88;
}
</style>