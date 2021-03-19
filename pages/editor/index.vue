<template>
  <div class="editor-page">
    <div class="container page">
      <div class="row">
  
        <div class="col-md-10 offset-md-1 col-xs-12">
          <form>
            <fieldset>
              <fieldset class="form-group">
                <input type="text" v-model="article.title" :disabled="disabled" class="form-control form-control-lg" placeholder="Article Title">
              </fieldset>
              <fieldset class="form-group">
                <input type="text" v-model="article.description" :disabled="disabled" class="form-control" placeholder="What's this article about?">
              </fieldset>
              <fieldset class="form-group">
                  <textarea class="form-control" v-model="article.body" :disabled="disabled" rows="8" placeholder="Write your article (in markdown)"></textarea>
              </fieldset>
              <fieldset class="form-group">
                  <input type="text" @keyup.enter="addTags" v-model="tag" :disabled="disabled" class="form-control" placeholder="Enter tags">
                  <div class="tag-list">
                    <span class="tag-default tag-pill" v-for="(tag,i) in article.tagList" :key="i">
                      <i class="ion-close-round" @click="article.tagList.splice(i, 1)"></i>
                      {{ tag }}
                    </span>
                  </div>
              </fieldset>
              <button @click="submit" :disabled="disabled" class="btn btn-lg pull-xs-right btn-primary" type="button">
                  Publish Article
              </button>
            </fieldset>
          </form>
        </div>
  
      </div>
    </div>
  </div>
</template>
<script>
  import { createArticle, updateArticle } from '@/api/article'
  import { getArticle } from '@/api/article'
  export default {
    // 在路由匹配组件渲染之前会先执行中间件处理
    middleware: 'authenticated',  // ['', '']
    name: 'EditorIndex',
    async asyncData({ params }) {
      const { data } = params.slug ? await getArticle(params.slug) : {}
      const { article } = params.slug ? data : {article: {
        title: null,
        description: null,
        body: null,
        tagList: []
      }}
      return {
        article,
        tag: null,
        disabled: false
      }
    },
    methods: {
      addTags () {
        if (this.tag && this.article.tagList.indexOf(this.tag) < 0) {
          this.article.tagList.push(this.tag)
          this.tag = null
        }
      },
      async submit () {
        this.disabled = true
        const { data } = this.article.slug ? await updateArticle(this.article.slug,this.article) : await createArticle({
          article: this.article
        })
        this.$router.push({
          name: 'article',
          params: {
            slug: data.article.slug
          }
        })
      }
    }
  }
</script>
<style>

</style>