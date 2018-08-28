<script type="text/javascript">
  import Vue from 'vue'
  import $ from 'jquery'
  import Echart from 'echarts'

  var specialBarChart;
  var pieColorArray = ['#ffd687','#87ffb7','#87b7ff','#9287ff','#ff8787','#e4b6ee','#fbc566','#b9fa97','#DA70D6'];
  var labelRight = {
        normal: {
            position: 'right',
            color:'#ffffff'
        }
    };
  var specialBarChartOption = {
    tooltip : {
        trigger: 'axis',
        axisPointer : {            // 坐标轴指示器，坐标轴触发有效
            type : 'shadow'        // 默认为直线，可选为：'line' | 'shadow'
        }
    },
    grid: {
        top: 0,
        bottom: 20,
        left:5,
        right:35
    },
    xAxis: {
        type : 'value',
        position: 'top',
        axisLabel : {
          show: true,
          textStyle : {
            color : '#ffffff',
          },
          
        },
        splitLine: {lineStyle:{type:'dashed'}},
    },
    yAxis: {
        type : 'category',
        axisLine: {show: false},
        axisLabel: {show: false},
        axisTick: {show: false},
        splitLine: {show: false},
        data : [],
    },
    series : [
        {
            name:'生活费',
            type:'bar',
            stack: '总量',
            itemStyle:{
              normal:{
                color:function(params){
                    return  pieColorArray[params.dataIndex]
                }

              }
            },
            label: {
                normal: {
                    show: true,
                    formatter: '{b}'
                }
            },
            data:[
                {value: 20, label: labelRight},
                -30,
                {value: -20, label: labelRight},
                15
            ]
        }
    ]
};


  function init(specialBarData) {

	var gaugeChartContainer = document.getElementById('assetChart');

	specialBarChart = Echart.init(gaugeChartContainer);

	specialBarChart.setOption(specialBarChartOption);
}

function setData(specialBarData){
   var unit = specialBarData[0][0].unit;
   $("#unitId").text('单位：'+unit);
   if (unit == '亿'||unit =='万' ) {
        $("#unitId").text('单位：'+unit+ '元');
   }
   var parmData = []
   var yAxisData = []
   for (var i = 0; i < specialBarData.length; i++) {
        var obj = {value :specialBarData[i][0].value ,label:labelRight};
        yAxisData.push(specialBarData[i][0].value);
        parmData.push(obj);
    }
    specialBarChartOption.yAxis.data = yAxisData;
    specialBarChartOption.series[0].data =  parmData;
    specialBarChart.setOption(specialBarChartOption);
}
export default{
	data() {
		return {

		}
	},
	method() {
		return {
			init: init,
            setData:setData
		}
	}
}
</script>