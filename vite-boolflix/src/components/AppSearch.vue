<script>
import axios from "axios";
import { store } from "../store.js"; // Importo lo store

export default {
  computed: {
    store() {
      return store;
    },
  },
  methods: {
    onSearch() {
      if (this.store.searchedMovie.trim() !== "") {
        this.searchContent(); // Cambia il nome della funzione
      }
    },
    searchContent() {
      const apiKey = "45f1e974018f30249339945d2d29072a"; // Uso la mia chiave API
      const movieUrl = "https://api.themoviedb.org/3/search/movie";
      const tvUrl = "https://api.themoviedb.org/3/search/tv";

      // Effettua entrambe le chiamate API
      Promise.all([
        axios.get(movieUrl, {
          params: {
            api_key: apiKey,
            query: this.store.searchedMovie,
            language: "it-IT",
          },
        }),
        axios.get(tvUrl, {
          params: {
            api_key: apiKey,
            query: this.store.searchedMovie,
            language: "it-IT",
          },
        }),
      ])
        .then(([movieResponse, tvResponse]) => {
          const movies = movieResponse.data.results;
          const tvShows = tvResponse.data.results;

          // Combina i risultati e normalizza i dati
          this.store.movieList = this.normalizeResults(movies, "movie").concat(
            this.normalizeResults(tvShows, "tv")
          );
        })
        .catch((error) => {
          console.error("Errore nella chiamata API:", error);
        });
    },
    // Funzione per normalizzare i risultati in un formato coerente
    normalizeResults(results, type) {
      const imageBaseUrl = "https://image.tmdb.org/t/p/w342"; // URL base per le immagini
      return results.map((item) => ({
        title: item.title || item.name,
        original_title: item.original_title || item.original_name,
        original_language: item.original_language,
        vote_average: item.vote_average,
        overview: item.overview,
        type: type,
        poster_path: item.poster_path
          ? `${imageBaseUrl}${item.poster_path}`
          : null,
      }));
    },
    getFlagUrl(languageCode) {
      const defaultFlagUrl =
        "https://upload.wikimedia.org/wikipedia/commons/a/ac/No_image_available.svg";
      if (!languageCode) return defaultFlagUrl;

      const languageToCountry = {
        en: "gb",
        da: "dk",
        ja: "jp",
        hi: "in",
        zh: "cn",
      };

      const countryCode = languageToCountry[languageCode] || languageCode;

      const url = `https://flagcdn.com/w320/${countryCode}.png`;

      const unsupportedLanguages = ["xx", "zzz"];
      return unsupportedLanguages.includes(languageCode) ? defaultFlagUrl : url;
    },
    // Funzione per calcolare il numero di stelle
    calculateStars(voteAverage) {
      return Math.ceil(voteAverage / 2); // Arrotonda per eccesso
    },
  },
};
</script>

<template>
  <div class="input-group mb-3 align-items-center justify-content-between">
    <div>
      <a href=""><img src="../assets/netflix-logo.png" alt="" /></a>
    </div>
    <nav>
      <ul>
        <li><a href="#">Home</a></li>
        <li><a href="#">Serie TV</a></li>
        <li><a href="#">Film</a></li>
        <li><a href="#">Originali</a></li>
        <li><a href="#">Aggiunti di recente</a></li>
        <li><a href="#">La mia lista</a></li>
      </ul>
    </nav>
    <div class="d-flex">
      <input
        v-model="store.searchedMovie"
        @keyup.enter="onSearch"
        type="text"
        class="form-control"
        placeholder="Cerca un film o una serie TV..."
        aria-label="Recipient's username"
        aria-describedby="button-addon2"
      />
      <button @click="onSearch" class="" type="button" id="button-addon2">
        Cerca
      </button>
    </div>
  </div>

  <div v-if="store.movieList.length > 0" class="movie-results mt-4">
    <h3 class="text-center">Risultati della ricerca:</h3>
    <ul>
      <li v-for="(item, index) in store.movieList" :key="index" class="mb-3">
        <h4>{{ item.title }}</h4>
        <img
          v-if="item.poster_path"
          :src="item.poster_path"
          alt="Copertina"
          class="poster"
        />
        <p><strong>Titolo Originale:</strong> {{ item.original_title }}</p>
        <p>
          <strong>Lingua Originale:</strong>
          <img
            :src="getFlagUrl(item.original_language)"
            :alt="item.original_language"
            class="flag-icon"
          />
          {{ item.original_language.toUpperCase() }}
        </p>
        <p><strong>Voto:</strong> {{ item.vote_average }}/10</p>

        <!-- Stelle per il voto -->
        <p>
          <strong>Stelle:</strong>
          <span v-for="star in calculateStars(item.vote_average)" :key="star">
            <i class="fas fa-star"></i>
          </span>
          <span
            v-for="star in 5 - calculateStars(item.vote_average)"
            :key="star + 'empty'"
          >
            <i class="far fa-star"></i>
          </span>
        </p>

        <p class="text-center">
          <strong>Trama:</strong> <br />
          <span v-if="item.overview">{{ item.overview }}</span>
          <span v-else> Nessuna trama presente in archivio</span>
        </p>
        <p>
          <strong>Tipo:</strong>
          {{ item.type === "movie" ? "Film" : "Serie TV" }}
        </p>
      </li>
    </ul>
  </div>

  <div v-else class="fs-2 text-center" id="no-result">
    <p>
      Nessun risultato trovato. <br />Prova a cercare un altro film o una serie
      TV.
    </p>
  </div>
</template>

<style lang="scss" scoped>
@use "bootstrap/scss/bootstrap.scss" as *;

/* Stili personalizzati per le bandiere */
.flag-icon {
  width: 24px;
  height: 18px;
  margin-right: 8px;
  vertical-align: middle;
}

/* Stili per le copertine */
.poster {
  width: 100px;
  height: auto;
  margin-right: 10px;
}

/* Stili per i risultati */
.movie-results {
  ul {
    list-style-type: none;
    padding: 50px;

    li {
      padding: 1em;
      border: 1px solid #ddd;
      border-radius: 5px;
      margin-bottom: 10px;
      display: flex;
      align-items: center;
      flex-direction: column;
    }
  }
}

/* Stile per le stelle */
i {
  color: gold;
}
</style>
