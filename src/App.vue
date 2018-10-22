<template>
  <div id="app">
    <h3>Example 1</h3>
    <div>
      Data: {{ example1 }}
    </div>
    <button @click="getLanguage">Get Language</button>
    <hr>

    <h3>Example 2</h3>
    <div>
      Data:
      <div :key="champion" v-for="champion in champions">
        {{ champion }}
      </div>
    </div>
    <button @click="getChampions">Get Champions</button>
    <hr>

    <h3>Example 3</h3>
    Name: <input v-model="championName">
      <div>
        Data:{{ champion }}
      </div>
    <button @click="getChampionByName">Get Champion</button>
    <hr>

    <h3>Example 4</h3>
    Name: <input v-model="name">
    Attack Damage: <input v-model.number="attack">
    <div>
      Data:
      {{ updatedChampion }}
    </div>
    <button @click="updateAttackDamage">Update Champion</button>
  </div>
</template>
<script>
import axios from "axios";
export default {
  name: "app",
  data() {
    return {
      name: "",
      example1: "",
      champions: [],
      champion: {},
      championName: "Ashe",
      updatedChampion: {},
      attack: 5.5
    };
  },
  methods: {
    async getLanguage() {
      try {
        const { data } = await axios.post("http://localhost:4000/graphql", {
          query: "{ language }"
        });
        this.example1 = data.data.language;
      } catch (e) {
        console.log("err", e);
      }
    },
    async getChampions() {
      const { data } = await axios.post("http://localhost:4000/graphql", {
        query: `{
          getChampions {
            name
          }
        }`
      });
      this.champions = data.data;
    },
    async getChampionByName() {
      const { data } = await axios.post("http://localhost:4000/graphql", {
        query: `
      query GetChampionByName($championName: String!) {
        getChampionByName(name: $championName) {
          name
          attackDamage
        }
      }`,
        variables: {
          championName: this.championName
        }
      });
      this.champion = data.data.getChampionByName;
    },
    async updateAttackDamage() {
      const { data } = await axios.post("http://localhost:4000/graphql", {
        query: `
        mutation UpdateAttackDamage(
          $championName: String!,  $attackDamage: Float) {
          updateAttackDamage(name: $championName, attackDamage: $attackDamage) {
            name
            attackDamage
          }
        }`,
        variables: {
          championName: this.name,
          attackDamage: this.attack
        }
      });
      this.updatedChampion = data.data.updateAttackDamage;
    }
  }
};
</script>