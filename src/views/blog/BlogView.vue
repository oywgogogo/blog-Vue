<template>
  <div class="me-view-body" v-title :data-title="title">

    <el-container class="me-view-container">
      <!--<el-aside class="me-area">-->
        <!--<ul class="me-operation-list">-->
          <!--<li class="me-operation-item">-->
            <!--<el-button type="primary" icon="el-icon-edit"></el-button>-->
          <!--</li>-->
        <!--</ul>-->
      <!--</el-aside>-->
      <el-aside class="cardme">
        <card-me></card-me>
      </el-aside>
      <el-main>

        <div class="me-view-card">

          <div class="me-view-author" >
            <h1 class="me-view-title">{{article.title}}</h1>
            <a class="">
              <img class="me-view-picture" :src="article.avatar"></img>
            </a>
            <div class="me-view-info">
              <span>{{article.author}}</span>
              <div class="me-view-meta">
                <span>{{article.createDate | format}}</span>&nbsp;&nbsp;&nbsp;
                <span><i class="iconfont icon-yuedu"></i>   {{article.viewCounts}}</span>&nbsp;&nbsp;
                <span><i class="iconfont icon-pinglun"/>   {{article.commentCounts}}</span>
              </div>

            </div>
            <el-button
              size="mini"
              v-if= isAuthor
              @click="editArticle()"
              style="position: absolute;left: 65%; top: 200px"
              round
              icon="el-icon-edit" type="success">编辑</el-button>
          </div>
          <div class="me-view-content">
            <markdown-editor :editor=article.editor></markdown-editor>
          </div>

          <div class="me-view-end">
            <el-alert
              title="文章End..."
              type="success"
              center
              :closable="false">
            </el-alert>
          </div>

          <div class="info">
            <div class="me-view-tag">
              标签：
              <!--<el-tag v-for="t in article.tags" :key="t.id" class="me-view-tag-item" size="mini" type="success">{{t.tagName}}</el-tag>-->
              <el-button @click="tagOrCategory('tag', t.id)" size="mini" type="primary" v-for="t in article.tags" :key="t.id" round plain>{{t.tagName}}</el-button>
            </div>

            <div class="me-view-category">
              文章分类：
              <!--<span style="font-weight: 600">{{article.category.categoryName}}</span>-->
              <el-button @click="tagOrCategory('category', article.category.id)" size="mini" type="primary" round plain>{{article.category.categoryName}}</el-button>
            </div>
          </div>

          <div class="me-view-comment">
            <div class="me-view-comment-write">
              <el-row :gutter="20">
                <el-col :span="2">
                  <a class="">
                    <img class="me-view-picture" :src="avatar"></img>
                  </a>
                </el-col>
                <el-col :span="22">
                  <el-input
                    type="textarea"
                    :autosize="{ minRows: 2}"
                    placeholder="你的评论..."
                    class="me-view-comment-text"
                    v-model="comment.content"
                    resize="none">
                  </el-input>
                </el-col>
              </el-row>

              <el-row :gutter="18" >
                <el-col :span="1" :offset="21">
                  <el-button type="success" @click="publishComment()">评论</el-button>
                </el-col>
              </el-row>
            </div>

            <div class="me-view-comment-title">
              <span>{{article.commentCounts}} 条评论</span>
            </div>

            <commment-item
              v-for="(c,index) in comments"
              :comment="c"
              :articleId="article.id"
              :index="index"
              :rootCommentCounts="comments.length"
              @commentCountsIncrement="commentCountsIncrement"
              :key="c.id">
            </commment-item>

          </div>

        </div>
      </el-main>
      <el-aside class="el-aside-right view_right" >

        <!--        <card-me class="me-area"></card-me>-->
        <card-tag :tags="hotTags" style="border-radius: 10px"></card-tag>

        <card-article cardHeader="最热文章" :articles="hotArticles" style="border-radius: 10px; margin-top: 20px" ></card-article>

        <card-archive cardHeader="文章归档" :archives="archives" style="border-radius: 10px; margin-top: 20px"></card-archive>

        <card-article cardHeader="最新文章" :articles="newArticles" style="border-radius: 10px; margin-top: 20px"></card-article>

      </el-aside>
    </el-container>
  </div>
</template>

<script>

  import CardMe from '@/components/card/CardMe'
  import MarkdownEditor from '@/components/markdown/MarkdownEditor'
  import CommmentItem from '@/components/comment/CommentItem'
  import {viewArticle} from '@/api/article'
  import {getCommentsByArticle, publishComment} from '@/api/comment'

  import default_avatar from '@/assets/img/default_avatar.png'

  import CardArticle from '@/components/card/CardArticle'
  import CardArchive from '@/components/card/CardArchive'
  import CardTag from '@/components/card/CardTag'
  import ArticleScrollPage from '@/views/common/ArticleScrollPage'

  import {getArticles, getHotArtices, getNewArtices} from '@/api/article'
  import {getHotTags} from '@/api/tag'
  import {listArchives} from '@/api/article'

  export default {
    name: 'BlogView',
    created() {
      this.getArticle()
      this.getHotArtices()
      this.getNewArtices()
      this.getHotTags()
      this.listArchives()

    },
    watch: {
      '$route': 'getArticle'
    },

    data() {
      return {
        hotTags: [],
        hotArticles: [],
        newArticles: [],
        archives: [],
        article: {
          id: '',
          title: '',
          commentCounts: 0,
          viewCounts: 0,
          summary: '',
          author: {},
          tags: [],
          category:{},
          avatar: '',
          createDate: '',
          editor: {
            value: '',
            toolbarsFlag: false,
            subfield: false,
            defaultOpen: 'preview'
          }

        },
        isAuthor: false,
        articleAuthor: '',
        comments: [],
        comment: {
          article: {},
          content: ''
        }
      }
    },

    computed: {
      avatar() {
        let avatar = this.$store.state.avatar

        if (avatar.length > 0) {
          return avatar
        }
        return default_avatar
      },
      title() {
        return `${this.article.title} - 文章 - oyw`
      }
    },
    methods: {
      getHotArtices() {
        let that = this
        getHotArtices().then(data => {
          that.hotArticles = data.data
        }).catch(error => {
          if (error !== 'error') {
            that.$message({type: 'error', message: '最热文章加载失败!', showClose: true})
          }

        })

      },
      getNewArtices() {
        let that = this
        getNewArtices().then(data => {
          that.newArticles = data.data
          console.log(that.newArticles)
        }).catch(error => {
          if (error !== 'error') {
            that.$message({type: 'error', message: '最新文章加载失败!', showClose: true})
          }

        })

      },
      getHotTags() {
        let that = this
        getHotTags().then(data => {
          that.hotTags = data.data
        }).catch(error => {
          if (error !== 'error') {
            that.$message({type: 'error', message: '最热标签加载失败!', showClose: true})
          }

        })
      },
      listArchives() {
        listArchives().then((data => {
          this.archives = data.data
        })).catch(error => {
          if (error !== 'error') {
            this.$message({type: 'error', message: '文章归档加载失败!', showClose: true})
          }
        })
      },

      //---------------------------------------------------
      tagOrCategory(type, id) {
        this.$router.push({path: `/${type}/${id}`})
      },
      editArticle() {
        this.$router.push({
          path: `/write/${this.article.id}`,
          query: {
            isUpdate: true
          }
        })
      },
      getArticle() {
        let that = this
        viewArticle(that.$route.params.id).then(resp => {
          // Object.assign(that.article, data.data)
          that.article = resp.data;
          console.log(resp.data)
          console.log("***********")
          that.article.editor =  {
            value: '',
            toolbarsFlag: false,
            subfield: false,
            defaultOpen: 'preview'
          }

          that.article.editor.value = resp.data.body.content
          that.articleAuthor = resp.data.authorId

          that.getCommentsByArticle()


          console.log(this.$store.state.id)
          console.log(this.articleAuthor)
          console.log('!!!!!!!!!!!!!!')
          if (this.$store.state.id === this.articleAuthor){
            this.isAuthor = true
          }
        }).catch(error => {
          if (error !== 'error') {
            that.$message({type: 'error', message: '文章加载失败', showClose: true})
          }
        })
      },
      publishComment() {
        let that = this
        if (!that.comment.content) {
          return;
        }
        that.comment.article.id = that.article.id
        let parms = {articleId:that.article.id,content:that.comment.content}
        publishComment(parms,this.$store.state.token).then(data => {
          if(data.success){
            that.$message({type: 'success', message: '评论成功', showClose: true})
            // this.reload()
            that.comment.content = ''
            that.comments.unshift(data.data)
            that.commentCountsIncrement()

          }else{
               that.$message({type: 'error', message: data.msg, showClose: true})
          }

        }).catch(error => {
          if (error !== 'error') {
            that.$message({type: 'error', message: '评论失败', showClose: true})
          }
        })
        this.$router.go(0)

      },

      getCommentsByArticle() {
        let that = this
        getCommentsByArticle(that.article.id).then(data => {
          if(data.success){
               that.comments = data.data
          }else{
             that.$message({type: 'error', message: '评论加载失败', showClose: true})
          }
        }).catch(error => {
          if (error !== 'error') {
            that.$message({type: 'error', message: '评论加载失败', showClose: true})
          }
        })
      },
      commentCountsIncrement() {
        this.article.commentCounts += 1
      }
    },
    components: {
      'markdown-editor': MarkdownEditor,
      CommmentItem,
      'card-me': CardMe,
      'card-article': CardArticle,
      'card-tag': CardTag,
      ArticleScrollPage,
      CardArchive
    },
    //组件内的守卫 调整body的背景色
    beforeRouteEnter(to, from, next) {
      window.document.body.style.backgroundColor = '#fff';
      next();
    },
    beforeRouteLeave(to, from, next) {
      window.document.body.style.backgroundColor = '#f5f5f5';
      next();
    }
  }
</script>

<style>
  .me-view-body {
    margin: 100px auto 140px;
    width: 100%;
  }

  .me-view-container {
    width: 100%;
    /*width: 100%;*/
  }

  .cardme {
    margin-top: 20px;
    margin-left: 60px;
    width: 260px!important;
    /*border-radius: 10px;*/
  }

  .el-main {
    /*margin-top: 20px;*/
    overflow: hidden;
  }

  .view_right {
    margin-top: 20px;
    margin-right: 70px;
  }


  .me-view-card {
    width: 700px;
  }

  .me-view-title {
    text-align: center;
    font-size: 34px;
    font-weight: 800;
    line-height: 1.3;
  }

  .me-view-author {
    /*margin: 30px 0;*/
    /*margin-top: 10px;*/
    vertical-align: middle;
    border:1px solid #e3e197;
    background:#fff;
    /*border-radius: 5px;*/

  }

  .me-view-picture {
    width: 40px;
    height: 40px;
    border: 1px solid #ddd;
    border-radius: 50%;
    vertical-align: middle;
    background-color: #5fb878;
  }

  .me-view-info {
    display: inline-block;
    vertical-align: middle;
    margin-left: 8px;
  }

  .me-view-meta {
    font-size: 14px;
    color: #343030;
  }

  .me-view-end {
    margin-top: 20px;
  }

  .info {
    border-left: 4px solid #c5cac3;
    background:#ffffdd;
    border-radius: 5px;
  }

  .me-view-tag {
    padding-top: 5px;
    margin-top: 20px;
    padding-left: 6px;


  }

  .me-view-category {
    padding-top: 5px;
    padding-left: 6px;
  }

  .me-view-tag-item {
    margin: 0 4px;
  }

  .me-view-comment {
    margin-top: 60px;
  }

  .me-view-comment-title {
    font-weight: 600;
    border-bottom: 1px solid #f0f0f0;
    padding-bottom: 20px;
  }

  .me-view-comment-write {
    margin-top: 20px;
  }

  .me-view-comment-text {
    font-size: 16px;
  }

  .v-show-content {
    padding: 8px 25px 15px 30px !important;
  }

  .v-note-wrapper .v-note-panel {
    box-shadow: none !important;
  }

  .v-note-wrapper .v-note-panel .v-note-show .v-show-content, .v-note-wrapper .v-note-panel .v-note-show .v-show-content-html {
    background: #fff !important;
  }

  .me-view-content {
    /*margin-top: 20px;*/

  }


</style>
