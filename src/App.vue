<template>
  <div class="container">
    <header>
      <h1>BlueSky Explorer</h1>
      <div>
        <input type="text" v-model="searchQuery" @input="debouncedSearch" placeholder="Rechercher un post..">
      </div>
    </header>
    <main>
      <aside>
        <h2>Recherches</h2>
        <button @click="addNewSearch" >Enregistrer la recherche</button>
        <ul>
          <li v-for="search in savedSearches" :key="search.name">
            <button @click="useSearch(search.query)" >{{ search.name }}</button>
          </li>
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
          :timestamp="formatTimestamp(post.indexedAt)"
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
import dayjs from 'dayjs';
import relativeTime from 'dayjs/plugin/relativeTime'
import { debounce } from 'lodash';

dayjs.extend(relativeTime)

export default {
  name: 'App',
  components: {
    PostCard
  },

  data() {
    return {
      posts: [],
      searchQuery: '',
      savedSearches: [
        { "name": "Actualités Football", "query": "domain:lemonde.fr football" },
        { "name": "CSS examples", "query": "css code -#CodePenChallenge" },
        { "name": "Vaccins", "query": "vaccin -covid" }
]
    }
  },
  
  created() {
    this.debouncedSearch = debounce(this.fetchPosts, 1000)
  },

  methods: {

    async fetchPosts() {
      const response = await axios.get('https://public.api.bsky.app/xrpc/app.bsky.feed.searchPosts',{
        params: {
          q: 'lang:fr '+ this.searchQuery, limit: 20
        }
      })
      this.posts = response.data.posts
    },

    formatTimestamp(timestamp) {
      return dayjs(timestamp).fromNow()
    },

    useSearch(query) {
      this.searchQuery = query;
      this.fetchPosts()
    },

    addNewSearch() {
      const name = prompt("Donnez un nom a votre recherche:")
      if (name) {
        this.savedSearches.push({ name, query: this.searchQuery })
      }
    }

  },

  mounted() {
    this.fetchPosts()
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
