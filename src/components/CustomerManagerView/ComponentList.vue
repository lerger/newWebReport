<script>
import Vue from 'vue'
import $ from 'jquery'

var componentList = Vue.extend({
  template: `
  <div v-for="item in params">
    <li>  
        <label for="folder{{item.no}}" class="folderOne">{{item.orgName}}</label> <input type="checkbox" id="folder{{item.no}}" /> 
          
    </li> 
  </div> 
  `,
  data: function() {
    return { 

    }
  },

  methods: {
 
  },
  props: [
    'params'
  ],
  
  
  ready () {
    
  }
});

Vue.component('componentList', componentList);

export default {
  data () {
    return {
      
    }
  }
}

</script>

<style type="text/css">


</style>