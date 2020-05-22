<template>
  <div id="app">
    <p>Type to search Wikipedia</p>
    <input v-model="search" />
    <div v-if="results">
      <h1>Wiki search result for : {{ results.term }}</h1>
      <ul style="list-style: none;" v-if="results.matches.length">
        <li v-for="match in results.matches" :key="match.url">
          <a :href="match.url">{{ match.title }}</a>
          <p>{{ match.description }}</p>
        </li>
      </ul>
      <p v-else> No matches found.</p>
    </div>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import { ajax } from "rxjs/ajax";
import { pluck, filter, debounceTime, distinctUntilChanged, switchMap, map } from "rxjs/operators";

const baseUrl = "https://en.wikipedia.org/w/api.php?action=opensearch&format=json&origin=*";

// 利用rxjs內建的ajax去拿api資料
function fetchTerm(searchTerm) {
  return ajax.getJSON(`${baseUrl}&search=${searchTerm}`);
}

// 拿回的資料返回一個object
function formatResult(res) {
  return {
    term: res[0],
    matches: res[1].map((title, i) => ({
      title: title,
      description: res[2][i],
      url: res[3][i]
    }))
  };
}

export default {
  name: "App",
  components: {
    // HelloWorld
  },
  data() {
    return {
      search: ""
    };
  },
  subscriptions() {
    return {
      // $watchAsObservable 會根据一个值的侦听器创建 observable。值会以 { newValue, oldValue } 的格式触发
      results: this.$watchAsObservable("search").pipe(
        pluck("newValue"), // 选择属性来发出，這邊是拿到newValue才發出
        filter(text => text.length > 2),  // 要大於2
        debounceTime(300),  // 至少輸入後0.3 s
        distinctUntilChanged(), // 只有當拿到的值跟上一個值不同才發出
        switchMap(fetchTerm), // 完成前一个内部 observable，发出值
        map(formatResult) // 对源 observable 的每个值应用投射函数
      )
    };
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

</style>