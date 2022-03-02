<template>
  <el-card class="el-card1">
    <div style="text-align: center">
      <img class="me-header-picture-cardMe" src="static/cat.png"/>
    </div>
    <p style="text-align: center">天青色</p>
    <div class="el-divider el-divider--horizontal"></div>
    <div class="me-author-tool">
      <i @click="showTool(qq)" :title="qq.title" class="iconfont icon-qq"></i>
      <i @click="showTool(bilibili)" :title="bilibili.title" class="iconfont icon-bilibili-fill"></i>
      <i @click="showTool(wechat)" :title="wechat.title" class="iconfont icon-logo-wechat"></i>
      <i @click="showTool(github)" :title="github.title" class="iconfont icon-github"></i>
    </div>

    <div class="me-author-description">
      <div class="card_me_item"><i class="el-icon-location-outline"></i> &nbsp;江西&赣州</div>
      <div class="card_me_item"><i class="iconfont icon-gerenzhiyeguihua"></i> &nbsp;java后端摸鱼猿</div>
      <div class="card_me_item"><i class="iconfont icon-aihao"></i> &nbsp;乒乓球 <a href="https://www.bilibili.com/video/BV1uT4y1P7CX?share_source=copy_web" target="_blank">GTA6(点击获取)</a></div>
    </div>




  </el-card>

</template>

<script>
  export default {
    name: 'CardMe',
    data() {
      return {
        qq: {title: 'QQ', message: '2226477173'},
        bilibili: {
          title: '哔哩哔哩',
          message: '<a target="_blank" href="https://www.bilibili.com">https://www.bilibili.com</a>'
        },
        wechat: {title: 'wechat', message: 'oyw18479746332'},
        github: {title: 'github', message: '<a target="_blank" href="https://github.com/oywgogogo">我的github传送门</a>'},
      }

    },
    methods: {
      showTool(tool) {
        this.$message({
          duration: 0,
          showClose: true,
          dangerouslyUseHTMLString: true,
          message: '<strong>' + tool.message + '</strong>',
          type: "success"
        });
      },

    }
  }
</script>

<style scoped>
  .me-author-name {
    text-align: center;
    font-size: 30px;
    border-bottom: 1px solid #5FB878;
  }

  .me-author-description {
    padding: 8px 0;
  }

  .me-icon-job {
    padding-left: 16px;
  }

  .me-author-tool {
    /*text-align: center;*/
    /*padding-top: 10px;*/
  }

  .me-author-tool i {
    cursor: pointer;
    padding: 4px 10px;
    font-size: 30px;
  }

  .el-card1 {
    /*margin-top: 200px;*/
  }

  .me-header-picture-cardMe {
    width: 55px;
    height: 55px;
    /*border: 1px solid #ddd;*/
    border-radius: 50%;
    vertical-align: middle;
    text-align: center;
  }

  .el-divider--horizontal {
    display: block;
    height: 1px;
    width: 100%;
    margin: 24px 0;
  }
  .el-divider {
    /*height: 1px;*/
    background-color: #dcdfe6;
    position: relative;
  }

  .card_me_item {
    line-height: 40px;
    height: 40px;
    border-top: 1px solid #dcdfe6;
  }
</style>
