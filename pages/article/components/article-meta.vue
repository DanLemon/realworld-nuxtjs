<template>
  <div class="article-meta">
    <nuxt-link :to="{
      name: 'profile',
      params: {
        username: article.author.username
      } 
    }">
      <img :src="article.author.image" />
    </nuxt-link >
    <div class="info">
      <nuxt-link :to="{
        name: 'profile',
        params: {
          username: article.author.username
        } 
      }" class="author">
        {{ article.author.username }}
      </nuxt-link>
      <span class="date">{{ article.createdAt | date('MMM DD,YYYY') }}</span>
    </div>
    <button 
      v-if="article.author.username !== $store.state.user.username"
      class="btn btn-sm btn-outline-secondary"
      :class="{
        active: article.author.following
      }"
    >
      <i class="ion-plus-round"></i>
      &nbsp;
     {{ article.author.following ? 'Unfollow' : 'Follow' }} {{ article.author.username }}
    </button>
    <nuxt-link v-else class="btn btn-outline-secondary btn-sm" :to="{
      name: 'editor',
      params: {
        slug: article.slug
      }
    }">
      <i class="ion-edit"></i> Edit Article
    </nuxt-link>
    &nbsp;&nbsp;
    <button v-if="article.author.username !== $store.state.user.username" class="btn btn-sm btn-outline-primary" :class="{
      active: article.favorited
    }">
      <i class="ion-heart"></i>
      &nbsp;
      {{ article.favorited ? 'Unfavorite' : 'Favorite' }} Article <span class="counter">({{article.favoritesCount}})</span>
    </button>
    <button v-else class="btn btn-outline-danger btn-sm" @click="delArticle(article.slug)">
      <i class="ion-trash-a"></i> Delete Article
    </button>
  </div>
</template> 
<script>
  import { deleteArticle } from '@/api/article'
  export default {
    name: 'ArticleMeta',
    props: {
      article: {
        type: Object,
        required: true
      }
    },
    methods: {
      async delArticle (slug) {
        await deleteArticle(slug)
        this.$router.push('/')
      }
    }
  }
</script>
<style>

</style>