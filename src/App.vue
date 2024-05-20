<script>
import { ref, onMounted } from 'vue'
import axios from 'axios'
import ShimmerLoader from './components/ShimmerLoader.vue'
import ErrorMessage from './components/ErrorMessage.vue'

export default {
  name: 'App',
  components: {
    ShimmerLoader,
    ErrorMessage
  },
  setup() {
    const characters = ref([])
    const loading = ref(true)
    const error = ref(true)
    const currentPage = ref(1)
    const pagesCount = ref(0)
    const name = ref('')
    const status = ref('')

    const fetchCharacters = async (page) => {
      loading.value = true
      try {
        const params = {
          page,
          name: name.value,
          status: status.value
        }
        const response = await axios.get('https://rickandmortyapi.com/api/character', { params })
        characters.value = response.data.results
        pagesCount.value = response.data.info.pages
        error.value = false
      } catch (err) {
        console.error(err)
        error.value = true
      } finally {
        loading.value = false
      }
    }
    const handleSearch = () => {
      fetchCharacters(1)
    }
    const handleNextPage = () => {
      currentPage.value++
      fetchCharacters(currentPage.value)
    }

    const handlePrevPage = () => {
      currentPage.value--
      fetchCharacters(currentPage.value)
    }
    const handleFirstPage = () => {
      currentPage.value = 1
      fetchCharacters(1)
    }
    const handleLastPage = () => {
      currentPage.value = pagesCount.value
      fetchCharacters(pagesCount.value)
    }
    const resetFilters = () => {
      name.value = ''
      status.value = ''
      fetchCharacters(1)
    }
    onMounted(() => {
      fetchCharacters()
    })

    return {
      characters,
      loading,
      error,
      currentPage,
      pagesCount,
      name,
      status,
      handleSearch,
      handleNextPage,
      handlePrevPage,
      handleFirstPage,
      handleLastPage,
      resetFilters
    }
  }
}
</script>

<template>
  <header>
    <div>
      <a href="https://rickandmortyapi.com/" target="blank"> Rick & Morty </a>
    </div>
  </header>
  <main>
    <form @submit.prevent="handleSearch">
      <label>Name <input v-model="name" placeholder="Rick" /></label>
      <label
        >Status
        <select v-model="status">
          <option value="">Any</option>
          <option value="alive">Alive</option>
          <option value="dead">Dead</option>
          <option value="unknown">Unknown</option>
        </select></label
      >
      <button type="submit">Apply</button>
      <button @click="resetFilters">Reset</button>
    </form>
    <ShimmerLoader :loading="loading" />
    <ErrorMessage :error="error" />
    <div v-if="!loading && !error">
      <div class="character-cards_wrapper">
        <div class="card" v-for="character in characters" :key="character.id">
          <div class="card__layer card__layer1">
            <div class="content">
              <img class="card__image" :src="character.image" :alt="character.name" />
              <h3 class="card__title">{{ character.name }}</h3>
            </div>
          </div>
          <div class="card__layer card__layer2">
            <div class="content card__description-wrapper">
              <div class="card__description">
                <p class="card__description-title">Status:</p>
                <p class="card__description-info">{{ character.status }}</p>
              </div>
              <div class="card__description">
                <p class="card__description-title">Species:</p>
                <p class="card__description-info">{{ character.species }}</p>
              </div>
              <div v-if="character.type" class="card__description">
                <p class="card__description-title">Type:</p>
                <p class="card__description-info">{{ character.type }}</p>
              </div>
              <div class="card__description">
                <p class="card__description-title">Gender:</p>
                <p class="card__description-info">{{ character.gender }}</p>
              </div>
              <div class="card__description">
                <p class="card__description-title">Origin:</p>
                <p class="card__description-info">{{ character.origin.name }}</p>
              </div>
              <div class="card__description">
                <p class="card__description-title">Last seen:</p>
                <p class="card__description-info">{{ character.location.name }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="pagination">
        <div class="button-wrapper">
          <button
            class="pagination-button button-left"
            @click="handleFirstPage"
            :disabled="currentPage === 1"
          >
            <img class="button-img" src="./assets/images/arrow-first.svg" alt="to the first page" />
          </button>
          <button
            class="pagination-button button-left"
            @click="handlePrevPage"
            :disabled="currentPage === 1"
          >
            <img
              class="button-img"
              src="./assets/images/arrow-prev.svg"
              alt="to the previous page"
            />
          </button>
        </div>
        <span class="pages-count">{{ currentPage }}/{{ pagesCount }}</span>
        <div class="button-wrapper">
          <button
            class="pagination-button"
            @click="handleNextPage"
            :disabled="currentPage === pagesCount"
          >
            <img class="button-img" src="./assets/images/arrow-next.svg" alt="to the next page" />
          </button>
          <button
            class="pagination-button"
            @click="handleLastPage"
            :disabled="currentPage === pagesCount"
          >
            <img class="button-img" src="./assets/images/arrow-last.svg" alt="to the last page" />
          </button>
        </div>
      </div>
    </div>
  </main>
  <footer>
    <div>
      <a href="https://github.com/EmiliiaJohnson" target="blank"> By Emiliia </a>
    </div>
  </footer>
</template>

<style scoped>
form {
  width: 300px;
  align-self: center;
  background-color: var(--base-color);
  padding: 10px 20px;
  display: flex;
  flex-direction: column;
}
label {
  display: flex;
  flex-direction: column;
}
input,
select {
  font-family: 'Typewriter', Arial, Helvetica, sans-serif;
  font-size: 16px;
  margin: 10px 0;
  height: 2rem;
  padding: 0 10px;
  border: 1px solid #333333;
  transition: var(--transition);

  &:focus {
    outline: none;
    box-shadow: 0px 0px 0px 1px rgba(0, 0, 0, 1);
  }
  &::placeholder {
    color: #9c9c9c;
  }
}
select {
  cursor: pointer;
}
button {
  font-family: 'Typewriter', Arial, Helvetica, sans-serif;
  font-size: 16px;
  margin: 10px 0;
  height: 2rem;
  padding: 0 10px;
  border: 1px solid #333333;
  transition: all 0.3s ease;
  background-color: #fff;
  cursor: pointer;
  &:hover {
    box-shadow: 0px 0px 0px 1px rgba(0, 0, 0, 1);
  }
  &:active {
    box-shadow: 0px 0px 0px 2px rgba(0, 0, 0, 1);
  }
}

.character-cards_wrapper {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  justify-items: center;
  gap: 0 1rem;
}
.card {
  position: relative;
  cursor: pointer;
  transition: var(--transition);
}
.card__layer {
  width: 300px;
  height: 200px;
  transition: 0.5s;
}
.card__layer1 {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1;
  transform: translateY(100px);
}
.card:hover .card__layer1 {
  transform: translateY(0);
}
.card__image {
  width: 300px;
  height: 300px;
  position: relative;
}
.card__title {
  font-family: 'Typewriter-bold', Arial, Helvetica, sans-serif;
  width: fit-content;
  padding: 0 10px;
  background-color: var(--base-color);
  color: #000000;
  position: absolute;
  top: 100%;
}
.card__layer2 {
  position: relative;
  background: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
  box-sizing: border-box;
  transform: translateY(-100px);
  height: 12em;
  margin-top: 22px;
}
.card:hover .card__layer2 {
  transform: translateY(24px);
}
.character-cards_wrapper:hover > .card:not(:hover) {
  opacity: 0.7;
}
.card:hover .card__description-info {
  animation: typing 1.2s steps(25) 0.2s forwards;
}
.card__description-wrapper {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  width: 100%;
}
.card__description {
  font-family: 'Typewriter-Bold', Arial, Helvetica, sans-serif;
  padding: 5px 10px;
  margin: 0;
  overflow: hidden;
}
.card__description-title {
  background-color: var(--base-color);
  width: fit-content;
}
.card__description-info {
  font-family: 'Typewriter', Arial, Helvetica, sans-serif;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}
.pagination {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
}
.button-wrapper {
  display: flex;
}
.pagination-button {
  margin: 0 10px;
  border: none;
  height: 3rem;
  width: 3rem;
  background-color: var(--base-color);
  cursor: pointer;
  transition: var(--transition);
  &:hover {
    border-radius: 6px;
    transform: scale(1.1);
    box-shadow: -2px 3px 0px 0px rgba(0, 0, 0, 1);
    background-color: #fbfaf7;
    .button-img {
      transform: translateX(5px);
    }
  }
  &.button-left:hover {
    .button-img {
      transform: translateX(-5px);
    }
  }

  &:active {
    transform: scale(1);
    box-shadow: none;
  }
  &:disabled {
    background-color: #848484;
    cursor: auto;
    &:hover {
      box-shadow: none;
      border-radius: 0;
      transform: scale(1);
      .button-img {
        transform: translateX(0px);
      }
    }
  }
}
.button-img {
  transition: var(--transition);
  height: 1.5rem;
  width: 1.5rem;
}
.pages-count {
  font-size: 2em;
  color: #f2edd7;
}
header,
footer {
  background-color: #121212;
  height: 90px;
  display: flex;
  align-items: center;
  justify-content: center;
}
@keyframes typing {
  from {
    width: 0;
  }
  to {
    width: 100%;
  }
}
</style>
