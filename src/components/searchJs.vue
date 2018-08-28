<script type="text/javascript">
 import $ from 'jquery'
 import ParamData from '../js/params_data'

var cellArray = [];
function createLetter(cellArray){
        var Initials=$('.initials');
        var LetterBox=$('#letter');
        var initialssss = [];
        for (var i = 0; i < cellArray.length; i++) {
          
            initialssss.push(cellArray[i].letter);
        };
        var appendLi = '';
        for (var i = 0; i < initialssss.length; i++) {
            if (initialssss[i] != '最新指标') {
                var li = `<li>${initialssss[i]}</li>`
                appendLi += li;
            }
            
        };
       
        Initials.find('ul').append(appendLi);
        
        $(".initials ul li").click(function(){
            var _this=$(this);
            var LetterHtml=_this.html();
            LetterBox.html(LetterHtml).fadeIn();
            
            setTimeout(function(){
                LetterBox.fadeOut();
            },100);

            var _index = _this.index()
            if(_index==0){
                $('html,body').animate({scrollTop: '0px'}, 300);
            }else if(_index==initialssss.length -1 && initialssss.indexOf('#') != -1){
                var DefaultTop=$('#default').position().top;
                $('html,body').animate({scrollTop: DefaultTop+'px'}, 300);//点击最后一个如果存在＃滚到#号
            }else{
                var letter = _this.text();

                if($('#'+letter).length>0){

                    var LetterTop = $('#'+letter).position().top;
                   
                    $('html,body').animate({scrollTop: LetterTop-5+'px'}, 300);
                }
            }
        })

        var windowHeight=$(window).height();

        var InitHeight=windowHeight-94
        Initials.height(InitHeight)
        var LiHeight=InitHeight/29;
        Initials.find('li').height(LiHeight);

        var  InitialsHeight = Initials.find('ul').height();
      
        var maginTop = (windowHeight - InitialsHeight)/2 + 40;
     
        var InitialsFont = windowHeight*0.0163;
        Initials.css('font-size',InitialsFont+'px');

}

  

export default {
    data () {
      return {
        cellArray:cellArray,
      }
    },
    method () {
      return {
      
        createLetter: createLetter, 
      }
    }
  }
</script>