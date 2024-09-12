<template>
  <div class="container">
    <header>
      <h1 class="app-title" >BlueSky Explorer</h1>
      <div class="search-bar" >
        <input type="text" v-model="searchQuery" @input="debouncedSearch" placeholder="Rechercher un post..">
      </div>
    </header>
    <main>
      <aside>
        <h2 class="search-title" >Recherches</h2>
        <button class="add-search-btn"  @click="addNewSearch" >Enregistrer la recherche</button>
        <ul class="saved-searches" >
          <li v-for="search in savedSearches" :key="search.name">
            <button class="saved-searches-btn" @click="useSearch(search.query)" >{{ search.name }}</button>
          </li>
        </ul>
      </aside>
      <section class="posts" >
        <h2>Posts</h2>
        <div class="post-grid" >
          <PostCard
          v-for="post in posts"
          :key="post.id"
          :username="post.author.handle"
          :content="post.record.text"
          :timestamp="formatTimestamp(post.indexedAt)"
          :isSelected="selectedPosts.includes(post.uri)"
          @toggle="togglePost(post)"
          />
        </div>
      </section>
      <aside class="export-aside" >
        <h2 class="export-title" >Export</h2>
        <p class="selection-count" > {{ exportMessage() }} </p>
        <button 
          class="export-btn"
          @click="exportToExcel" 
          :disabled="selectedPosts.length === 0" 
          >Exporter
        </button>
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
import * as XLSX from 'xlsx'

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
      selectedPosts: [],
      allSelectedPosts: {},
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
    },

    togglePost(post) {
      const index = this.selectedPosts.indexOf(post.uri)
      if (index === -1) {
        this.selectedPosts.push(post.uri)
        this.allSelectedPosts[post.uri] = post
      } else {
        this.selectedPosts.splice(index, 1)
        delete this.allSelectedPosts[post.uri]
      }
    },

    exportMessage() {
      if (this.selectedPosts.length === 0) return 'Pas de favoris';
      if (this.selectedPosts.length === 1) return '1 post sélectionné';
      return this.selectedPosts.length + " posts sélectionnés"
    },

    exportToExcel() {
      const postsToExport = Object.values(this.allSelectedPosts)

      const rows = postsToExport.map(post => ({
        'Auteur': post.author.handle,
        'Contenu': post.record.text,
        'Date': post.indexedAt
      }))

      const worksheet = XLSX.utils.json_to_sheet(rows);
      const workbook = XLSX.utils.book_new();
      XLSX.utils.book_append_sheet(workbook, worksheet, "Posts");
      XLSX.writeFile(workbook, "Mes_Posts_favoris.xlsx");
    }

  },

  mounted() {
    this.fetchPosts()
  }

}
</script>

<style>

  :root {
    --primary-color: #1DA1F2;
    --background-color: #15202b;
    --text-color: #FFFFFF;
    --secondary-color: #657786;

  }

  body {
    background-color: var(--background-color);
    color: var(--text-color);
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  }

  .container {
    max-width: 1200px;
    margin: 0 auto;
  }

  header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 30px;
    padding-bottom: 20px;
    border-bottom: 1px solid var(--secondary-color);
  }

  .app-title {
    font-size: 40px;
    color: var(--primary-color);
    margin: 0;
  }

  .search-bar input {
    padding: 10px;
    width: 300px;
    background-color: var(--background-color);
    border: 1px solid var(--secondary-color);
    border-radius: 20px;
    color: var(--text-color);
    font-size: 15px;
  }

  .search-bar input:focus {
    outline: none;
    border-color: var(--primary-color);
  }

  main {
    display: grid;
    grid-template-columns: 200px 1fr 200px;
    gap: 20px;
  }

  .posts h2 {
    font-size: 25px;
    margin-bottom: 20px;
    color: var(--primary-color);
  }

  .post-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
    gap: 10px;
  }

  h2 {
    font-size: 25px;
    margin-bottom: 20px;
  }

  .add-search-btn {
    width: 100%;
    padding: 10px;
    background-color: var(--primary-color);
    color: white;
    border: none;
    border-radius: 20px;
    cursor: pointer;
    font-size: 13px;
  }

  .add-search-btn:hover {
    background-color: #1991db;
  }

  .saved-searches {
    list-style-type: none;
    padding: 0;
    margin-top: 20px;
  }

  .saved-searches-btn {
    width: 100%;
    padding: 10px;
    background-color: #22303c;
    color: white;
    border-radius: 10px;
    cursor: pointer;
    font-size: 13px;
  }

  .saved-searches-btn:hover {
    background-color: #2c3e50;
  }

  .selection-count {
    font-size: 15px;
    margin-bottom: 15px;
  }

  .export-btn {
    width: 100%;
    padding: 10px;
    background-color: var(--primary-color);
    color: white;
    border: none;
    border-radius: 20px;
    cursor: pointer;
  }

  .export-btn:hover {
    background-color: #1991db;
  }

  .export-btn:disabled {
    background-color: #38444d;
    cursor: not-allowed;
    opacity: 0.7;
  }

</style>
