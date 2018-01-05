<template>
  <main class="main-content">
    <section v-if="errors && errors.length" class="errors-section">
        <div class="container">
          <ul>
          <li v-for="error of errors">
            {{error.message}}
          </li>
        </ul>
        </div>
    </section>  
    <section v-if="loading" class="pokedex-loader">
      <div class="container">
        <h1 class="page-title">Loading...</h1>
      </div>
    </section>
    <section v-if="!loading" class="pokemon-details-section">
        <div class="container">
          <h1 class="main-title">{{ monster.id }}. <span class="monster-name">{{ monster.name }}</span></h1>
          <div class="panel">
              <div class="panel-body">
                  <div class="row">
                      <div class="col">
                          <img class="monster-image" v-if="monster.sprites" :src="monster.sprites.front_default" alt="">
                      </div>
                      <div class="col">
                          <table class="stats-table">
                              <tr>
                                  <th v-for="row in monster.stats">{{ row.stat.name }}</th>
                              </tr>
                              <tr>
                                <th v-for="row in monster.stats">{{ row.base_stat }}</th>
                              </tr>
                          </table>
                      </div>
                  </div>
              </div>  <!-- .panel-bpdy -->
              <div class="panel-footer">
                  <router-link :to="'/pokemon/' + prevId + '/'" class="back-link">&larr; Previous Pokemon</router-link>
                  <router-link to="/" class="back-link">Back to Pokemon List</router-link>
                  <router-link :to="'/pokemon/' + nextId + '/'" class="back-link">Next Pokemon &rarr;</router-link>
              </div>
          </div>
        </div>
    </section>
  </main>
</template>

<script>
import axios from "axios";

export default {
  name: "Monster",
  data() {
    return {
      loading: false,
      monster: null,
      errors: []
    };
  },
  mounted() {
    let vm = this;
    console.log("Monster mounted");
  },
  methods: {
    getMonster(){
      let vm = this;
      this.error = this.monster = null;
      vm.loading = true
      axios.get(`https://pokeapi.co/api/v2/pokemon/${vm.$route.params.id}`)
        .then(response => {
          // JSON responses are automatically parsed.
          console.log(response);
          //   this.totalResults = response.data.count;
          vm.monster = response.data;
          vm.loading = false;
        })
        .catch(e => {
          vm.errors.push(e);
          vm.loading = false;
        });
    }
  },
  watch: {
    '$route': 'getMonster'
  },
  created() {
    let vm = this;
    console.log("Monster created");
    vm.getMonster();
  },
  computed: {
    id(){
      return this.$route.params.id;
    },
    nextId(){
      return (parseInt(this.id) + 1).toString();
    },
    prevId(){
      return (parseInt(this.id) - 1).toString();
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h1,
h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  margin: 0 10px;
}
a {
  color: #42b983;
}

.container {
  max-width: 1280px;
  margin: 0 auto;
}

.main-title {
  font-size: 4rem;
  font-weight: 700;
}

.row {
  display: flex;
  .col {
    flex-grow: 1;
    flex-basis: 0;
  }
}

.monster-image {
  width: 500px;
  height: auto;
  display: block;
}

.panel-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.stats-table {
  border-collapse: collapse;
  width: 100%;

  td, th {
    padding: .5rem 1rem;
    text-align: left;
    border: 1px solid #e7e7e7;
    margin: 0;
  }
  
  th {
    // text-transform: uppercase;
    text-transform: capitalize;
  }
}

.monster-name {
    text-transform: capitalize;
}

</style>
