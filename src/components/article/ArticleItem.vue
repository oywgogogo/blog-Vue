<template>
  <el-card class="me-area" :body-style="{ padding: '16px' }">
    <div class="me-article-header">

      <a @click="view(id)" class="me-article-title">{{title}}</a>
      <el-button v-if="weight > 0" class="me-article-icon" type="text">置顶</el-button>

    </div>

    <div class="me-artile-description">
      {{summary}}
    </div>
    <div class="me-article-footer">


	  	<span class="me-article-author">
	    	<i class="me-icon-author"></i>&nbsp;{{author}}
	    </span>

      <el-tag v-for="t in tags" :key="t.tagName" size="mini" type="success">{{t.tagName}}</el-tag>
      <span class="me-pull-right me-article-count">
	    	<i class="iconfont icon-pinglun"></i>&nbsp;{{commentCounts}}
	    </span>
      <span class="me-pull-right me-article-count">
	    	<i class="iconfont icon-yuedu"></i>&nbsp;{{viewCounts}}
	    </span>
      <span class="me-pull-right me-article-count">
	    	<i class="el-icon-time"></i>&nbsp;{{createDate | format}}
	    </span>

    </div>
    <div class="read">
      <el-button type="success" round @click="view(id)">
        阅读全文
      </el-button>
    </div>
  </el-card>
</template>

<script>
  import { formatTime } from "../../utils/time";

  export default {
    name: 'ArticleItem',
    props: {
      id: String,
      weight: Number,
      title: String,
      commentCounts: Number,
      viewCounts: Number,
      summary: String,
      author: String,
      tags: Array,
      createDate: String
    },
    data() {
      return {}
    },
    methods: {
      view(id) {
        this.$router.push({path: `/view/${id}`})
      }
    }
  }
</script>

<style scoped>

  .me-article-header {
    text-align: center;
    /*padding: 10px 18px;*/
    padding-bottom: 10px;
    font-size: 20px;
  }

  .me-article-title {
    text-align: center;
    font-weight: 600;
  }

  .me-article-icon {
    padding: 3px 8px;
  }

  .me-article-count {
    color: #a6a6a6;
    padding-left: 14px;
    font-size: 13px;
  }

  .me-pull-right {
    float: right;
  }

  .me-artile-description {
    text-align: center;
    font-size: 16px;
    line-height: 24px;
    margin-bottom: 10px;
  }

  .me-article-author {
    color: #a6a6a6;
    padding-right: 18px;
    font-size: 16px;
  }

  .el-tag {
    margin-left: 6px;
  }

  .read {
    text-align: center;
    margin-top: 20px;
  }

</style>
