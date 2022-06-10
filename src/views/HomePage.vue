<template>
  <div class="container mt-5" >

    <b-form @submit="onSubmit" @reset="onReset">
      <h1>Spotify Search</h1>

      <b-form-group  class="mb-3" id="input-group-2" label="Your search:" label-for="input-2">
        <b-form-input
            id="input-2"
            v-model="form.name"
            placeholder="Enter search name"
            required
        ></b-form-input>
      </b-form-group>

      <b-form-group class="mb-3" id="input-group-3" label="Type:" label-for="input-3">
        <b-form-select class="form-control"
                       id="input-3"
                       v-model="form.type"
                       :options="types"
        ></b-form-select>
      </b-form-group>

      <b-button type="submit" variant="primary">Submit</b-button>
      <b-button type="reset" variant="danger">Reset</b-button>

    </b-form>
<div v-if="!error">
    <div class="row" v-if="artists.length > 0">
      <h2 class="mb-2 mt-3">Artists </h2> <hr/>
      <div class="col-md-3" v-for="(artist,i) in artists" :key = 'i'>
        <div class="card mb-5" style="">
          <img :src="artist.images.length > 0 ? artist.images[0].url : ''" class="card-img-top" alt="..."  style="height:210px; object-fit: cover; ">
          <div class="card-body">
            <h5 class="card-title">{{artist.name}}</h5>

            <a :href="artist.external_urls.spotify" class="btn btn-primary" target="_blank">Open in Spotify</a>
          </div>
        </div>

      </div>
    </div>
    <div v-if = 'isloading'>No search results found</div>

  <div class="row" v-if="tracks.length > 0">
      <h1>tracks</h1>
      <div class="col-md-3" v-for="(track,i) in tracks" :key = 'i'>
        <div class="card mb-5" style="">
          <img :src="track.images.length > 0 ? track.images[0].url : ''" class="card-img-top" alt="..."  style="height:210px; object-fit: cover; ">
          <div class="card-body">
            <h5 class="card-title">{{track.name}}</h5>

            <a :href="track.external_urls.spotify" class="btn btn-primary" target="_blank">Open in Spotify</a>
          </div>
        </div>
      </div>
    </div>

  <div class="row" v-if="albums.length > 0">
    <h1>albums</h1>
    <div class="col-md-3" v-for="(album,i) in albums" :key = 'i'>
      <div class="card mb-5" style="">
        <img :src="album.images.length > 0 ? album.images[0].url : ''" class="card-img-top" alt="..."  style="height:210px; object-fit: cover; ">
        <div class="card-body">
          <h5 class="card-title">{{album.name}}</h5>

          <a :href="album.external_urls.spotify" class="btn btn-primary" target="_blank">Open in Spotify</a>
        </div>
      </div>
    </div>
    </div>
  </div>
    <div v-else>
      {{error_msg}}
    </div>


  </div>
</template>

<script>
import axios from 'axios';
export default {
  name:'Home-Page',
  data() {
    return {
      form: {
        name: '',
        type: null,

      },
      types: [{ text: 'Select One', value: null },
        "album",
        "artist",
        "track",
      ],
      artists:[],
      tracks:[],
      albums:[],
      isloading:false,
      error:false,
      error_msg:""

    }
  },
  methods: {
    onSubmit(event) {
      event.preventDefault()
      axios.get(`http://127.0.0.1:8001/api/search?name=${this.form.name} ${this.form.type ? '&type='+this.form.type : ''}`).then((res) => {

        console.log(res)
        const {data} = res;

        if(data.artists){
          this.artists = data.artists.items;
          this.isloading = data.artists.items.length < 1
        }
        if(data.tracks){
          this.tracks = data.tracks.items;
        }
        if(data.albums){
          this.albums = data.albums.items;
        }
        if(data.playlists){
          this.albums = data.albums.items;
        }

        if(data.error){
          this.error = true;
          this.error_msg = data.error.message;
        }

      }).catch(()=> {
      this.error = true;
      this.error_msg = "Error while fetching data";
          }
      )

    },
    onReset(event) {
      event.preventDefault()
      // Reset our form values

      this.form.name = ''
      this.form.type = null
      // Trick to reset/clear native browser form validation state
      this.show = false
      this.$nextTick(() => {
        this.show = true
      })
    }
  }
}
</script>