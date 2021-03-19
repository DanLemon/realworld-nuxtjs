<template>
  <div class="profile-page">

    <div class="user-info">
      <div class="container">
        <div class="row">
  
          <div class="col-xs-12 col-md-10 offset-md-1">
            <img :src="profile.image" class="user-img" />
            <h4>{{ profile.username }}</h4>
            <p>
              {{ profile.bio }}
            </p>
            <button 
              v-if="profile.username !== $store.state.user.username" 
              class="btn btn-sm btn-outline-secondary action-btn"
              @click="onFollow(profile)"
              :disabled="profile.folllowDisabled"
              :class="{
                active: profile.following
              }"
            >
              <i class="ion-plus-round"></i>
              &nbsp;
              {{ profile.following ? 'Unfollow' : 'Follow' }} {{ profile.username }}
            </button>
            <nuxt-link v-else class="btn btn-sm btn-outline-secondary action-btn" to="/settings">
              <i class="ion-gear-a"></i> Edit Profile Settings
            </nuxt-link>
          </div>
  
        </div>
      </div>
    </div>
  
    <div class="container">
      <div class="row">
  
        <div class="col-xs-12 col-md-10 offset-md-1">
          <div class="articles-toggle">
            <ul class="nav nav-pills outline-active">
              <li class="nav-item">
                <nuxt-link class="nav-link"
                  :class="{active: tab === 'my_articles'}"
                  exact
                  :to="{
                    name: 'profile',
                    query: {
                      tab: 'my_articles'
                    }
                  }">My Articles</nuxt-link>
              </li>
              <li class="nav-item">
                <nuxt-link class="nav-link"
                :class="{active: tab === 'favorited_article'}"
                exact
                :to="{
                  name: 'profile',
                  query: {
                    tab: 'favorited_article'
                  }
                }">Favorited Articles</nuxt-link>
              </li>
            </ul>
          </div>

          <div class="article-preview" v-for="article in articles" :key="article.slug">
            <div class="article-meta">
              <nuxt-link :to="{
                name: 'profile',
                params: {
                  username: article.author.username
                }
              }">
                <img :src="article.author.image" />
              </nuxt-link>
              <div class="info">
                <nuxt-link :to="{
                  name: 'profile',
                  params: {
                    username: article.author.username
                  }
                }" class="author">{{ article.author.username }}
                </nuxt-link>
                <span class="date">{{ article.createdAt | date('MMM DD,YYYY')}}</span>
              </div>
              <button 
                class="btn btn-outline-primary btn-sm pull-xs-right"
                @click="onFavorite(article)"
                :disabled="article.favoriteDisabled"
                :class="{
                  active: article.favorited
                }"
              >
                <i class="ion-heart"></i> {{ article.favoritesCount }}
              </button>
            </div>
            <nuxt-link
              class="preview-link"
              :to="{
                name: 'article',
                params: {
                  slug: article.slug
                }
              }"
            >
              <h1>{{ article.title }}</h1>
              <p>{{ article.description }}</p>
              <span>Read more...</span>
            </nuxt-link>
          </div> 
  
  
        </div>
      </div>
    </div>
  
  </div>
</template>
<script>
  import { getProfile, followUser, unFollowUser } from '@/api/profile'
  import { getArticles } from '@/api/article'
  export default {
    middleware: 'authenticated',
    name: 'UserProfile',
    watchQuery: ['tab'],
    async asyncData ({ params, query }) {
      const page = Number.parseInt(query.page || 1)
      const limit = 5
      const tab = query.tab || 'my_articles'
      const obj = tab === 'my_articles' ? { 
        limit,
        offset: (page - 1) * limit,
        author: params.username
       } : { 
        limit,
        offset: (page - 1) * limit,
        favorited: params.username
      }

      const [ articleRes, profileRes ] = await Promise.all([
        getArticles(obj),
        getProfile(params.username)
      ])
      console.log(articleRes,profileRes)
      const { profile } = profileRes.data
      const { articles } = articleRes.data
      articles.forEach(article => article.favoriteDisabled = false)
       
      return {
        profile,
        articles,
        limit,
        page,
        tab: query.tab || 'my_articles'
      }
    },
    methods: {
      async onFollow (profile) {
        // console.log(article)
        profile.followDisabled = true
        if (profile.following) {
          // 取消关注
          await unFollowUser(profile.username)
          profile.following = false
          profile.followDisabled += -1
        } else {
          // 添加关注
          await followUser(profile.username)
          profile.following = true
          profile.followDisabled += 1
        }
        profile.followDisabled = false
      }
    }
  }
</script>
<style>

</style>