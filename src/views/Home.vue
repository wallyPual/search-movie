<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png" />
    <h1>영화 검색</h1>
    <div class="form">
      <form @submit.prevent="handleSubmit">
        <div class="column">
          <input type="text" class="input-text" v-model="movie" placeholder="영화명" />
        </div>
        <button type="submit" class="button" :disabled="loading || disabled">
          <span v-if="!loading">검색</span>
          <div v-else class="loader">
            <font-awesome-icon :icon="['fas', 'fan']" :style="{ color: '#fff', fontSize: 22 }" />
          </div>
        </button>
      </form>
    </div>
    <div>
      <div v-if="result !== null" class="result">검색결과 {{result}}건</div>
      <div class="card-contents">
        <a :href="item.link" target="_blank" class="card" v-for="(item, idx) in items" :key="idx">
          <div>
            <img :src="item.image" alt />
          </div>
          <div>
            <strong class="title" v-html="item.title"></strong>
            <br />
            <span class="sub-title" v-html="item.subtitle"></span>
          </div>
          <div class="rating">
            <span>평점:</span>
            <span>{{item.userRating}}</span>
          </div>
        </a>
      </div>
    </div>
  </div>
</template>

<script>
// import _ from 'lodash';
import axios from 'axios';

export default {
  name: 'home',
  data() {
    return {
      movie: '',
      disabled: true,
      loading: false,
      items: [],
      result: null
    };
  },
  components: {},
  methods: {
    handleSubmit() {
      this.getMovie();
    },
    async getMovie() {
      this.loading = true;
      const {
        statusText,
        data: { items, display }
      } = await axios.get(
        `/v1/search/movie.json?query=${this.movie}&display=20&start=1`,
        {
          headers: {
            'X-Naver-Client-Id': process.env.VUE_APP_OPTIONS_CLIENT_ID,
            'X-Naver-Client-Secret': process.env.VUE_APP_OPTIONS_CLIENT_SECRET
          }
        }
      );
      this.$data.items = items;
      if (statusText === 'OK') {
        this.items = items;
        this.loading = false;
        this.result = display;
      }
    }
  },
  watch: {
    movie(text) {
      this.watchResult = '입력중...';
      this.disabled = !text.length > 0;
    },
    items() {
      this.loading = this.items.length < 0;
    }
  },
  computed: {
    fullName: {
      // getter
      get() {
        return `${this.firstName} ${this.lastName}`;
      },
      // setter
      set(newValue) {
        const [firstName, lastName] = newValue.split(' ');
        this.firstName = firstName;
        this.lastName = lastName;
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.form {
  max-width: 400px;
  margin: 0 auto;
}
.column {
  display: flex;
  flex-direction: column;
  &--row {
    margin-top: 10px;
    flex-direction: row;
  }
}
.input-text {
  padding-left: 10px;
  height: 50px;
  border-radius: 5px;
  border: 1px solid #ccc;
  font-size: 15px;
  & + & {
    margin-top: 10px;
  }
}
.button {
  margin-top: 10px;
  width: 100%;
  height: 50px;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #42b983;
  color: #fff;
  font-size: 18px;
  &:disabled {
    opacity: 0.7;
  }
}
.card-contents {
  margin: 50px 0;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}
.card {
  display: block;
  width: 25%;
  color: #000;
  text-decoration: none;
  &:hover {
    .title,
    .sub-title {
      color: #42b983;
    }
  }
  img {
    max-width: 100%;
  }
  .sub-title {
    font-size: 14px;
    font-weight: 500;
  }
  .rating {
    margin-top: 10px;
  }
}
.result {
  margin-top: 50px;
  font-size: 20px;
  font-weight: bold;
}

.loader {
  animation-duration: 1s;
  animation-fill-mode: forwards;
  animation-iteration-count: infinite;
  animation-name: loader;
  animation-timing-function: linear;
}

@media screen and (max-width: 768px) {
  .card {
    width: calc(100% / 3);
  }
}

@media screen and (max-width: 549px) {
  .card {
    width: calc(100% / 2);
  }
}

@keyframes loader {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
</style>
