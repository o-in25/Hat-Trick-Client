<template>
  <div id="app">
    <h1>Player ratings</h1>
    <div v-for="player in data" v-bind:key="player.PlayerID">
      <p><span>PlayerID</span> {{data.PlayerID}}</p>
      <p><span>Rating</span> {{data.Rating}}</p>
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
      data:[]
    }
  },
  methods: {

  },
  created() {
    let playerRatings = null;
    let activePlayers = null;
    let playerStats = null;

    return new Promise((resolve, reject) => {
      axios.get('http://localhost:3000/api/ratings/pg').then((playerRatingData) => {
        resolve(playerRatingData.data);
      })
    }).then((playerRatingData) => {
      playerRatings = playerRatingData;
      return new Promise((resolve, reject) => {
        axios.get('http://localhost:3000/api/stats/activeplayers/position/pg').then((activePlayerData) => {
          resolve(activePlayerData.data);
        })
      }).then((activePlayerData) => {
        activePlayers = activePlayerData;
        return new Promise((resolve, reject) => {
          axios.get('http://localhost:3000/api/stats/cumulativeplayerstats/position/pg').then((cumulativePlayerData) => {
            resolve(cumulativePlayerData.data);
          })
        }).then((cumulativePlayerData) => {
          playerStats = cumulativePlayerData;
          return new Promise((resolve, reject) => {
            if(playerStats != null && activePlayers != null  && playerRatings != null) {
              resolve([playerStats, activePlayers, playerRatings]);
            } else {
            }
          }).then((arr) => {
            let playerStats_ = arr[0];
            let activePlayers_ = arr[1];
            let playerRatings_ = arr[2];
            console.log('here');
            let payload = [];
            //let playerRatings = null;
            //let activePlayers = null;
            //let playerStats = null;
            for(let i = 0; i < activePlayers_.length; i++) {
              // current is the active player at the ith index
              let current = activePlayers_[i];

              let currentPlayerId = current.ID;
              let match1 = null;
              let match2 = null;
              for(let i = 0; i < playerStats_.length; i++) {
                let el = playerStats_[i];
                // has a player id
                if(el.hasOwnProperty('PlayerID')) {
                  // has a player id that matches
                  if(el.PlayerID === currentPlayerId) {
                    match1 = el;
                    break;
                  }
                }
              }
              for(let i = 0; i < playerRatings_.length; i++) {
                let el = playerRatings_[i];
                // has a player id
                if(el.hasOwnProperty('PlayerID')) {
                  // has a player id that matches
                  if(el.PlayerID === currentPlayerId) {
                    match2 = el;
                    break;
                  }
                }
              }
              if(current != null && match1 != null && match2 != null) {
                let result = {profile: current, stats: match1, ratings: match2};
                payload.push(result);
              }
            }
            console.log(payload)
          })
        }).catch((err) => {
          throw Error(err);
        })
      })
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
