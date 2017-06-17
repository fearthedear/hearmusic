<template>
  <div class="main">
    <h1>{{ msg }}</h1>

    <div class="row">
      <template v-for="artist in songs">
        <div class="col s12 m5 l3">

          <v-card medium>
            <div class="card-image waves-effect waves-block waves-light">
              <img class="activator" :src="artist[0].user.avatar_url">
            </div>
            <div class="card-content">
              <span class="card-title activator grey-text text-darken-4">{{artist[0].user.username}}<i class="material-icons right">more_vert</i></span>
              <p></p>
            </div>
            <div class="card-reveal">
              <span class="card-title grey-text text-darken-4">{{artist[0].user.username}}<i class="material-icons right">close</i></span>            
              <v-collection>
              <template v-for="song in artist">        
                <v-collection-avatar :src="song.artwork_url">
                  <a :href="song.stream">
                  <span class="title">{{song.title}}</span>
                  <p>{{toMinutes(song.duration)}} <br>
                   
                  </p>
                  </a> 
                  <span slot="secondary"></span>
                </v-collection-avatar>
                
              </template>
              </v-collection>
            </div>
          </v-card>
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
          artistjson: null,
          artists: [],
          artworks: [],
          permalinks: [],
          songs: {}
        }
      },
      methods: {
        songArray: function (name) {
          return this.songs.name
        },
        toMinutes: function (time) {
          var hours = Math.floor(time / 3600)
          time = time - hours * 3600
          var minutes = Math.floor(time / 60)
          var seconds = time - minutes * 60
          function strPadLeft (string, pad, length) {
            return (new Array(length + 1).join(pad) + string).slice(-length)
          }
          var finalTime = strPadLeft(minutes, '0', 2) + ':' + strPadLeft(seconds, '0', 2)
          return finalTime
        }
      },
      created () {
        var _this = this
        $.getJSON('https://api-v2.hearthis.at/feed/?type=popular&page=1&count=20', function (json) {
          _this.artistjson = json
          insertArtists()
          getSongs(_this.permalinks)
        })
        var insertArtists = function () {
          _this.artistjson.forEach(track => {
            _this.artists.push(track.user.username)
            // _this.artworks.push(track.artwork_url)
            _this.artworks.push(track.user.avatar_url)
            _this.permalinks.push(track.user.permalink)
          })
          _this.artists = removeDuplicates(_this.artists)
          _this.artworks = removeDuplicates(_this.artworks)
          _this.permalinks = removeDuplicates(_this.permalinks)
        }

        var getSongs = function (links) {
          links.forEach(function (value) {
            var songjson = null
            $.getJSON('https://api-v2.hearthis.at/' + value + '/?type=tracks&page=1&count=5', function (json) {
              songjson = json
              insertSongs()
            })
            var insertSongs = function () {
              var allSongsByArtist = []
              songjson.forEach(track => {
                // var song = {}
                // song.push(track.title); song.push(track.duration); song.push(track.artwork_url); song.push(track.stream_url); song.push(track.user.username)
                allSongsByArtist.push(track)
              })
              // _this.songs.push(allSongsByArtist)
              var artist = allSongsByArtist[0].user.permalink
              _this.songs[artist] = allSongsByArtist
            }
          })
        }
        var removeDuplicates = function (array) {
          return Array.from(new Set(array))
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
      /*color: #42b983;*/
      color: inherit;
    }

    .card .card-image img {
      /*height: 200px;*/
    }
  </style>
