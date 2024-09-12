<template>
  <div class="container">
    <header>
      <h1>BlueSky Explorer</h1>
      <div>
        <input type="text" placeholder="Rechercher un post..">
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
      </section>
      <aside>
        <h2>Export</h2>
        <p>Pas de favoris</p>
      </aside>
    </main>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {
  
  }
}
</script>

<style>

  .container {
    max-width: 1200px;
  }

  header {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  main {
    display: flex;
    justify-content: space-between;
  }
</style>
