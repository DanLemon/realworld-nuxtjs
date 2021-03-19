<template>
  <div>
      <form class="card comment-form">
      <div class="card-block">
        <textarea class="form-control" v-model="comment.body" placeholder="Write a comment..." rows="3"></textarea>
      </div>
      <div class="card-footer">
        <img :src="$store.state.user.image" class="comment-author-img" />
        <button class="btn btn-sm btn-primary" @click="postComments">
        Post Comment
        </button>
      </div>
    </form>
    
    <div class="card" v-for="(comment,i) in comments" :key="comment.id">
      <div class="card-block">
        <p class="card-text">{{ comment.body }}</p>
      </div>
      <div class="card-footer">
        <nuxt-link :to="{ 
          name: 'profile', 
          params: {
            username: comment.author.username
          }
        }" class="comment-author">
          <img :src="comment.author.image" class="comment-author-img" />
        </nuxt-link>
        &nbsp;
        <nuxt-link :to="{ 
          name: 'profile', 
          params: {
            username: comment.author.username
          }
        }" class="comment-author">{{ comment.author.username }}</nuxt-link>
        <span class="date-posted">{{ comment.createdAt | date('MMM DD, YYYY') }}</span>
        <span class="mod-options" v-show="comment.author.username === article.author.username">
          <i class="ion-trash-a" @click="delComment(comment.id, i)"></i>
        </span>
      </div>
    </div>
  </div>
  
</template> 
<script>
  import { getComments, addComments, deleteComments } from '@/api/article'
  export default {
    name: 'ArticleComments',
    props: {
      article: {
        type: Object,
        required: true
      }
    },
    data () {
      return {
        comments: [],  // 文章列表
        comment: {
          body: null
        }
      }
    },
    async mounted () {
      const { data } = await getComments(this.article.slug)
      this.comments = data.comments
    },
    methods: {
      async postComments () {
        const { data } = await addComments(this.article.slug, this.comment)
      },
      async delComment (id,i) {
        await deleteComments(this.article.slug, id)
        this.comments.splice(i,1)
      }
    }
  }
</script>
<style>

</style>