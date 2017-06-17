<template>
  <div class="main">
    <h1>{{ msg }}</h1>

    
    
    <div class="row maincontent">
    <!-- <li v-for="xx in artistjson">{{xx}}</li> -->
      <template v-for="artist_username in songs">
        <div class="col s12 m5 l3">

          <v-card medium>
            <div class="card-image waves-effect waves-block waves-light">
              <img class="activator" :src="artist_username[0].user.avatar_url">
            </div>
            <div class="card-content">
              <span class="card-title activator grey-text text-darken-4">{{artist_username[0].user.username}}</span>
              <p>{{artist_username[0].genre}}
              <br>
              Tracks: {{ artist_username.length }}
              <br>
              Played: {{ playcount(artist_username) }}
              </p>
            </div>
            <div class="card-reveal">
              <span class="card-title grey-text text-darken-4">{{artist_username[0].user.username}}<i class="material-icons right">close</i></span>            
              <v-collection>
              <template v-for="song in artist_username">        
                <v-collection-avatar :src="song.artwork_url" class="songItem">
                  <div v-on:click="changeSong(song.stream_url)" class="songlink">
                  <span class="title">{{song.title}}</span>
                  <p>{{toMinutes(song.duration)}}</p>
                   </div>
                  <span slot="secondary"></span>
                </v-collection-avatar>
                
              </template>
              </v-collection>
            </div>
          </v-card>
          </div>
        </template>
      </div>
      <footer class="hidden">
            <iframe height="50px" :src="stream_playing"></iframe>
      </footer>
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
          permalinks: [],
          songs: {},
          stream_playing: null
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
        },
        changeSong: function (url) {
          this.stream_playing = url
          $('footer').removeClass('hidden')
        },
        playcount: function (allsongs) {
          var plays = 0
          allsongs.forEach(function (song) {
            plays = plays + parseInt(song.playback_count)
          })
          return plays
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
            _this.permalinks.push(track.user.permalink)
          })
          _this.artists = removeDuplicates(_this.artists)
          _this.permalinks = removeDuplicates(_this.permalinks)
        }

        var getSongs = function (links) {
          links.forEach(function (value) {
            var songjson = null
            $.getJSON('https://api-v2.hearthis.at/' + value + '/?type=tracks&page=1&count=20', function (json) {
              songjson = json
              insertSongs()
            })
            var insertSongs = function () {
              var allSongsByArtist = []
              songjson.forEach(track => {
                allSongsByArtist.push(track)
              })
              var artist = allSongsByArtist[0].user.permalink
              _this.songs[artist] = allSongsByArtist
              _this.$forceUpdate() // make vue realize object has changed.
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
    .songlink {
      cursor: pointer;
    }
    .maincontent {
      z-index: 1;
    }
    footer {
      z-index: 2;
      position: fixed; bottom: 0px; width: 100%; height: 26px; background-color: white;
    }
    iframe {
      border: none;
    }
    .hidden {
      display: none
    }
    .songItem {
      text-align: left;
      width: 100%;
    }
  </style>
