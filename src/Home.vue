<template>
  <div id="home">
    <el-container>

    	<base-header :activeIndex="activeIndex"></base-header>

		  <router-view class="me-container"/>

			<base-footer v-show="footerShow"></base-footer>

		</el-container>
    <div>
      <meting-js server="netease" type="playlist" id="7139531597" fixed="true" mini="true" ></meting-js>
    </div>
  </div>

</template>

<script>
import BaseFooter from '@/components/BaseFooter'
import BaseHeader from '@/views/BaseHeader'

export default {
  name: 'Home',
  data (){
  	return {
  			activeIndex: '/',
  			footerShow:true
  	}
  },
  components:{
  	'base-header':BaseHeader,
  	'base-footer':BaseFooter
  },
  beforeRouteEnter (to, from, next){
  	 next(vm => {
    	vm.activeIndex = to.path
  	})
  },
  beforeRouteUpdate (to, from, next) {
	  if(to.path == '/'){
	  	this.footerShow = true
	  }else{
	  	this.footerShow = false
	  }
	  this.activeIndex = to.path
	  next()
	}
}
</script>

<style>

#home{
  padding: 20px;
  font-family: "montserrat";
  background-image: linear-gradient(125deg,#d580ff, #ffaa00);
  background-size: 400%;
  animation: bganimation 10s infinite;

}
@keyframes bganimation {
  0% {
    background-position: 0% 50%;
  }

  50% {
    background-position: 100% 50%;
  }

  100% {
    background-position: 0% 50%;
  }
}


.me-container{
  margin: 120px auto 140px;
}
</style>
