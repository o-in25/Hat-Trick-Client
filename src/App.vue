<template>
  <div id="app">
    <h1>Player ratings</h1>
    <div v-for="player in playerRatings" v-bind:key="player.PlayerID">
      <p><span>PlayerID</span> {{player.PlayerID}}</p>
      <p><span>Rating</span> {{player.Rating}}</p>
      <hr/>
    </div>
  </div>
</template>

<script>/* eslint-disable */
import Vue from 'vue';
import axios from 'axios';

export default {
  name: 'App',
  data() {
    return {
      playerRatings: []
    }
  },
  methods: {},
  created() {
    console.log('called')
    axios.get('http://localhost:3000/api/ratings/pg').then(payload => {
      this.playerRatings = payload.data;
    }, err => {
      throw Error(err);
    });
  }
}
/* eslint-enable */
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 10px 20px 20px 10px;
}

p {
  text-align: left;
}
  p > span {
    font-weight: 900;
  }
</style>
