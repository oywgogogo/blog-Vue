<template>

  <el-header  class="el-header">
    <div class="blog-navbar" :style="opacityStyle" >

      <router-link style="text-decoration-line: none;color: white" to="/">
        <h3 style="display: inline;padding:20px 20px 20px 60px">天青色的Blog
        </h3>
      </router-link>

      <router-link  style="text-decoration-line: none;color: white;padding:20px;"
                   to="/">
        首页
      </router-link>



      <router-link  style="text-decoration-line: none;color: white;padding:20px"
                   to="/category/all">
        文章分类
      </router-link>
      <router-link  style="text-decoration-line: none;color: white;padding:20px"
                   to="/tag/all">
        标签
      </router-link>


      <router-link  style="text-decoration-line: none;color: white;padding:20px"
                   to="/archives">
        文章归档
      </router-link>

      <router-link  style="text-decoration-line: none;color: white;padding:20px"
                    to="/write">
        写文章
      </router-link>


      <template v-if="!user.login">

        <router-link  class="login_router"
                     to="/login">
          登录
        </router-link>

          <el-button type="text" v-show= false>注册</el-button>
      </template>

      <template v-else >
        <el-dropdown class="el-dropdown">
          <img class="me-header-picture" :src="user.avatar"/>
          <el-dropdown-menu>
            <el-dropdown-item @click.native="logout">
                退出
            </el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>


      </template>







    </div>
  </el-header>
</template>

<script>

  export default {

    name: 'BaseHeader',
    props: {
      activeIndex: String,
      simple: {
        type: Boolean,
        default: false
      }
    },
    data() {
      return {
        types: {},
        input: '',
        queryString: '',
        queryResult: [],
        timer: null,
        mobileHide: false,
        ifShowInput: false,
        opacityStyle:{ opacity:10 },
        i: 0
      }
    },
    mounted() {
      window.addEventListener('scroll',this.handleScroll,true)
    },
    computed: {
      user() {
        let login = this.$store.state.account.length != 0
        let avatar = this.$store.state.avatar
        return {
          login, avatar
        }
      }
    },

    methods: {
      logout() {
        let that = this
        this.$store.dispatch('logout').then(() => {
          this.$router.push({path: '/'})
        }).catch((error) => {
          if (error !== 'error') {
            that.$message({message: error, type: 'error', showClose: true});
          }
        })
      },
      handleScroll() {
        /*let top = document.documentElement.scrollTop //获取scroll偏移值
        let height_window = window.screen.height
        if(top > 45 && top <= height_window){   //实际按需求取范围
          let opacity = top / height_window    //对opacity作计算，透明度从起始到1随偏移值而改变
          this.opacityStyle.opacity -= opacity  //实时返回给opacityStyle
        }*/

        // 监听鼠标滚动
        // 页面滚动距顶部距离
        let height_window = window.screen.height
        var scrollTop = document.documentElement.scrollTop
        var scroll = scrollTop - this.i;
        this.i = scrollTop;
        console.log(scroll)
        if (scroll < 0) {
          // 鼠标上滚  执行的的方法
          this.opacityStyle.opacity -= scroll/50
        } else {
          // 鼠标下滚  执行的的方法
          this.opacityStyle.opacity -= scroll/50
        }
      }


    }
  }
</script>

<style>

  .el-header {
    position: fixed;
    /*z-index: 1024;*/
    min-width: 97.5%;
  }
  /*skymo's blog*/

  .blog-navbar {

    border-radius: 15px;
    height: 100px!important;
    line-height: 100px;
    font-size: 20px;
    /*padding-top:  20px;*/
    background-color: #333333;



    /*margin-left: auto !important;*/
    /*margin-right: auto !important;*/


  }
 .login_router {
   text-decoration-line: none;
   color: white;
   padding:20px;
   float:right;
   margin-right: 40px;
   line-height: 20px;
   font-size: 20px;
   color: #8affeb;
   margin-top: 20px;
   border: 1px solid #8affeb;
}

  .me-header-picture {
    float:right;
    margin-right: 40px;
    width: 46px;
    height: 46px;
    border: 1px solid #ddd;
    border-radius: 50%;
    vertical-align: middle;
    margin-top: 20px;
  }
  .el-dropdown{
    margin-top: 10px;
    float:right;
    margin-right: 40px;
  }
</style>
