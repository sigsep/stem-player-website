<template>
  <v-app id='app' :dark="dark">
     <v-toolbar>
      <v-toolbar-title><i>Open Unmix</i>: an open source music separation baseline for PyTorch and NNabla.</v-toolbar-title>

      <v-spacer></v-spacer>

      <v-toolbar-items>
        <v-btn @click.stop="dialog = true" text>More Info</v-btn>
      </v-toolbar-items>
        <v-btn icon>
          <v-icon>mdi-github-circle</v-icon>
        </v-btn>
    </v-toolbar>
      <v-dialog
      v-model="dialog"
      max-width="400"
      style="z-index: 100000000000"
    >
      <v-card>
        <v-card-title class="headline">A reference implementation for music separation</v-card-title>

        <v-card-text>
          <ul>
            <li>MIT-license</li>
            <li>pytorch, NNabla</li>
            <li>state-of-the-art DNN model [1]</li>
          </ul>
          <br>
          <h4>Features:</h4>
          
          <ul>
            <li>clean & modular python code</li>
            <li>dataloaders for <i>musdb</i> & arbitrary wav/mp3 folders</li>
            <li>standard pre-post processing: STFT, multichannel Wiener filters, etc.</li>
            <li>pretrained baseline model trained on [2]</li>
            <li>design your net, compare with state of the art easily</li>
          </ul>
          <br>
          <h3>Available on <v-btn text>https://open.unmix.app</v-btn></h3>

          <p>
          [1] Uhlich, Stefan, et al. "Improving music source separation based on deep neural networks through data augmentation and network blending." 2017 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP). IEEE, 2017.
          </p>
          <p>
          [2] Z. Rafii, A. Liutkus, F.-R. Stöter, S.I.  Mimilakis & R. Bittner (2017). The MUSDB18 corpus for music separation.
          </p>
        </v-card-text>

      </v-card>
    </v-dialog>
  <v-container>
    <v-row no-gutters>
        <v-select v-if="tracks.length > 1"
          :dark="dark"
          v-model="selectedTrack"
          class="select"
          :items="tracks"
          light
          label="Select track to separate"
        ></v-select>
      <Player :ref="player" :urls="tracklist" :dark="dark"></Player>
      <v-layout
        align-center
        justify-center
        style="background: red;"
      >
      </v-layout>
    </v-row>
    <v-row>
      <div class="logos" style="text-align:center">
         <span class="logo ">
           <a href="http://www.inria.fr">
             <img class='inria' src="./assets/logo_INRIA.png" alt="Inria" width="180px">
           </a>
         </span>
         <span class="logo">
           <a href="http://www.sony.com">
             <img src="./assets/sony.jpeg" alt="Sony" width="80px">
           </a>
         </span></br>
      </div>

    </v-row>
  </v-container>    
  <v-footer padless
  
  >
    
    <v-col
      class="text-center"
      cols="12"
      style="margin-left: 2em"
    >

      Copyright {{ new Date().getFullYear() }} — <strong>inria</strong>
    </v-col>
  </v-footer>
  </v-app>
</template>

<script>
import Player from './components/Player.vue'
import axios from 'axios'

export default {
  name: 'App',
  components: { Player },
  data () {
    return {
      dark: true,
      tracks: [],
      stems: [],
      selectedTrack: '',
      dialog: false,
      baseUrl: process.env.BASE_URL
    }
  },
  mounted: function () {
    this.fetchData();
  },
  created: function () {
    
  },
  methods: {
    fetchData(){
     axios.get(this.baseUrl + 'headers.json').then(response => {
        this.tracks = response.data.tracks
        this.stems = response.data.stems
        this.selectedTrack = response.data.selected_track
        this.dark = response.data.dark
     })
    }
  },
  computed: {
    tracklist: function () {
      var trackstoload = []
      for (let stem of this.stems) {
        trackstoload.push(
            { 'name': stem,
              'customClass': stem,
              'solo': false,
              'mute': false,
              'src': [
                'tracks', this.selectedTrack, stem
              ].join('/') + '.wav'
          })
      }
      return trackstoload
    }
  }

}
</script>

<style lang="stylus">
.logo {
  padding: 2em;
}

.logos {
  margin-top: 4em;

}

.select {
  z-index: 1000
}
</style>