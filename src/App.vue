<template>
  <div class="container">
    <header>
      <h1>BlueSky Explorer</h1>
      <div>
        <input type="text" v-model="searchQuery" @input="fetchPost" placeholder="Rechercher un post..">
      </div>
    </header>
    <main>
      <aside>
        <h2>Recherches</h2>
        <button>Enregistrer la recherche</button>
        <ul>
          <li>Actu foot</li>
          <li>Css ex</li>
          <li>Vaccins</li>
        </ul>
      </aside>
      <section>
        <h2>Posts</h2>
        <div class="post-grid" >
          <PostCard
          v-for="post in posts"
          :key="post.id"
          :username="post.author.handle"
          :content="post.record.text"
          :timestamp="post.indexedAt"
          />
        </div>
      </section>
      <aside>
        <h2>Export</h2>
        <p>Pas de favoris</p>
      </aside>
    </main>
  </div>
</template>

<script>
import axios from 'axios';
import PostCard from './components/PostCard.vue';


export default {
  name: 'App',
  components: {
    PostCard
  },

  data() {
    return {
      posts: [],
      searchQuery: ''
    }
  },

  methods: {

    async fetchPost() {
      const response = await axios.get('https://public.api.bsky.app/xrpc/app.bsky.feed.searchPosts',{
        params: {
          q: 'lang:fr '+ this.searchQuery, limit: 20
        }
      })
      this.posts = response.data.posts
    }

  },

  mounted() {
    this.fetchPost()
  }

}
</script>

<style>

  .container {
    max-width: 1200px;
    margin: 0 auto;
  }

  header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
  }

  main {
    display: grid;
    grid-template-columns: 200px 1fr 200px;
    gap: 20px;
  }

  .post-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
    gap: 10px;
  }

</style>
