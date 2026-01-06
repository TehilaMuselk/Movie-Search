<template>
  <div class="container">
    <h1>Movie Search App</h1>
    
    <div class="search-box">
      <input 
        v-model="searchTerm" 
        placeholder="Search for a movie (e.g., Inception)" 
        @keyup.enter="searchMovies"
      />
      <button @click="searchMovies">Search</button>
    </div>

    <div class="movies-grid">
      <div v-for="movie in movies" :key="movie.id" class="movie-card">
        <img 
          v-if="movie.poster_path" 
          :src="'https://image.tmdb.org/t/p/w300' + movie.poster_path" 
          :alt="movie.title + ' poster'"
        />
        <div class="info">
          <h3>{{ movie.title }}</h3>
          <p>{{ movie.release_date ? movie.release_date.slice(0, 4) : 'Year N/A' }}</p>
          <p class="overview">{{ movie.overview || 'No overview available.' }}</p>
        </div>
      </div>
    </div>

    <p v-if="!movies.length && searched">No movies found. Try another search!</p>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const apiKey = '66d071eb8ca69f083b76c7aa2ef868bc';  // ← Replace this!
const searchTerm = ref('');
const movies = ref([]);
const searched = ref(false);

const searchMovies = async () => {
  if (!searchTerm.value.trim()) return;
  
  searched.value = true;
  const url = `https://api.themoviedb.org/3/search/movie?api_key=${apiKey}&query=${encodeURIComponent(searchTerm.value)}`;
  
  try {
    const response = await fetch(url);
    const data = await response.json();
    movies.value = data.results || [];
  } catch (error) {
    console.error('Error fetching movies:', error);
    movies.value = [];
  }
};
</script>

<style>
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}
h1 { text-align: center; color: #333; }

.search-box {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin: 30px 0;
}
input {
  padding: 12px;
  font-size: 16px;
  width: 400px;
  border: 1px solid #ccc;
  border-radius: 8px;
}
button {
  padding: 12px 24px;
  background: #007bff;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}
button:hover { background: #0056b3; }

.movies-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
}
.movie-card {
  border: 1px solid #ddd;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
  background: white;
}
.movie-card img {
  width: 100%;
  height: auto;
}
.info {
  padding: 15px;
}
.info h3 { margin: 0 0 8px; font-size: 18px; }
.info p { margin: 5px 0; color: #666; font-size: 14px; }
.overview {
  font-size: 13px;
  line-height: 1.4;
  margin-top: 10px;
  color: #444;
}
</style>