<template>
  <div class="hello">
    <!-- <h1>{{ msg }}</h1>
    <p>Type to search Wikipedia</p>
    <div id="el">
      <input v-model="search">
      <div v-if="results">
        <h1>{{ results.term }}</h1>
        <ul v-if="results.matches.length">
          <li v-for="match in results.matches">
            <a :href="match.url">{{ match.title }}</a>
            <p>{{ match.description }}</p>
          </li>
        </ul>
        <p v-else>
          No matches found.
        </p>
      </div>
    </div> -->
  </div>
</template>

<script>

  export default {
    name: 'HelloWorld',
    props: {
      msg: String,
      
    },
     subscriptions () {
     return {
     // this is the example in RxJS's readme.
     results: this.$watchAsObservable('search').pipe(
     pluck('newValue'),
     filter(text => text.length > 2),
     debounceTime(500),
     distinctUntilChanged(),
     switchMap(fetchTerm),
     map(formatResult)
     )
     }
     }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  h3 {
    margin: 40px 0 0;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: inline-block;
    margin: 0 10px;
  }

  a {
    color: #42b983;
  }
</style>