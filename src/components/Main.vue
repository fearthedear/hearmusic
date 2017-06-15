<template>
  <div class="main">
    <h1>{{ msg }}</h1>
    
    <div class="row">
    <template v-for="(artist, index) in artists">
        <div class="col s12 m3 l2">
          <div class="card">
            <div class="card-image">
              <img v-bind:src="artworks[index]">
              <span class="card-title"></span>
            </div>
            <div class="card-content">
              <p>{{artist}}</p>
              </div>
              <div class="card-action">
                <a href="#">Listen Now</a>
              </div>
            </div>
          </div>
      </template>
    </div>

    </div>
  </template>

  <script>
    export default {
      name: 'main',
      data () {
        return {
          msg: 'Welcome to Hearmusic',
          json: null,
          artists: [],
          artworks: []
        }
      },
      created () {
        var _this = this
        $.getJSON('https://api-v2.hearthis.at/feed/?type=popular&page=1&count=20', function (json) {
          _this.json = json
          insertArtists()
        })
        var insertArtists = function () {
          _this.json.forEach(track => {
            _this.artists.push(track.user.username)
            // _this.artworks.push(track.artwork_url)
            _this.artworks.push(track.user.avatar_url)
          })
        }
      }
    }
  </script>

  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
    h1, h2 {
      font-weight: normal;
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

    .card .card-image {
      height: 200px;
    }
  </style>
