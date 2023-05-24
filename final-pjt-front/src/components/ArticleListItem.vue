<template>
  <div>
    <div class="articlelist-item-container">
      <p style="margin-top:20px;">
        Article #{{ articleDetail.id }}  
      </p>
      <hr>
      <!-- 제목 -->
      <h1 style="margin:20px;">
        게시글 제목: {{ articleDetail.title }}
      </h1>
      <p style="margin-left:200px;">작성자 :  <router-link :to="{ name: 'profile/:username', params: { username: articleDetail.username } }">{{ articleDetail.username }}</router-link></p>
      
      <h3 style="margin-top:20px;">내용</h3>
      <p>
        {{ articleDetail.content }}
      </p>
      <form @submit.prevent="deleteArticle">
        <input type="submit" value="DELETE">
      </form> 
      <router-link :to="{name : 'article/:id/update', params: {id: articleDetail.id}}">[UPDATE]</router-link>
      <p>{{ articleDetail.like_users.length }}명이 좋아합니다.</p>
    </div>
    
    <!-- <p>작성 시간 : {{ articleDetail.created_at }}</p>
    <p>수정 시간 : {{ articleDetail.updated_at }}</p> -->
    
    <div class="article-comment-container">
      <p>총 {{ articleDetail.comment_count }}개의 댓글이 있습니다.</p>
      <hr>
      <div v-for="comment in articleDetail.comment_set" :key="comment.id">
        <br>
      <!-- <p>댓글 번호 : {{ comment.id }}</p> -->
      
      <span>{{ comment.content }}</span>
      <span style="margin-left:100px;">작성자:<router-link :to="{ name: 'profile/:username', params: { username: comment.username } }">{{ comment.username }}</router-link></span>
      <!-- <p>댓글 작성 시간 : {{ comment.created_at }}</p>
      <p>댓글 수정 시간 : {{ comment.updated_at }}</p> -->
      <form @submit.prevent="deleteComment(comment.id)">
        <input type="submit" value="DELETE" class="comment-btn">
      </form>
      <router-link :to="{name : 'article/:id/comment/:commentid/update', params: {id: articleDetail.id, commentid: comment.id}}" class="comment-update-btn">
      <!-- style="padding:10px; height:30px; width: 90px; boader: solid 1px white;"> -->
      UPDATE</router-link>
      </div>

    </div>
    <hr style="margin-left:20px;">
    <p style="margin-left:20px;">
      <CommentCreate :articleId="articleId"/>
    </p>


  </div>
</template>

<script>
import CommentCreate from '@/components/CommentCreate'
import axios from 'axios'
const API_URL = 'http://127.0.0.1:8000'

export default {
  name: 'ArticleListItem',
  components: {
    CommentCreate,
  },
  props: {
    article: Object,
  },
  data() {
    return {
      articleId: null,
    }
  },
  created() {
    this.getArticleDetail()
  },
  computed: {
    articleDetail() {
      return this.$store.state.article
    },
    // articles() {
    //   return this.$store.state.articles
    // },

  },

  mounted() {
    this.articleId = this.$route.params.id
  },
  
  methods:{
    getArticleDetail() {
      const articleId = this.$route.params.id

      const payload = {
        articleId
      }

      this.$store.dispatch('getArticleDetail', payload)
    },

    deleteArticle() {
      const articleId = this.$route.params.id

      axios({
        method: 'delete',
        url: `${API_URL}/articles/${articleId}/`,
        data: { articleId },
        headers: {
          Authorization: `Token ${this.$store.state.token}`
        }
      })
      .then(() => {
        this.$router.push({ name : 'community' })
      })
      .catch((err => {
        console.log(err)
      }))
    },

    deleteComment(commentId) {
      axios({
        method: 'delete',
        url: `${API_URL}/articles/comments/${commentId}/`,
        headers: {
          Authorization: `Token ${this.$store.state.token}`
        }
      })
      .then(() => {
        // 새로고침
        location.reload()
      })
      .catch((err => {
        console.log(err)
      }))
    },
    
  }
}
</script>

<style>
.articlelist-item-container{
  color: black;
  display: flex;
  flex-direction: column;
  margin-left: 20px;
}
.article-comment-container{
  color: black;
  display: flex;
  flex-direction: column;
  margin-left: 20px;
}
.comment-btn{
  border: 1px solid #fff;
  background: none;
  cursor: pointer;
  margin-left: 25%;
  height: 30px;
  width: 90px;
}
.comment-btn:hover{
  background:#fff;
}
.comment-update-btn{
  border: 1px solid #fff;
  font-size: 10px;
  background: none;
  cursor: pointer;
  margin-left: 25%;
  padding:5px;
  height: 30px;
  width: 90px;
}
</style>
