<template>
  <div
    style="
      background-image: url('./fondo3.jpg');
      background-position: center;
      background-repeat: repeat;
    "
  >
  <br>
    <v-btn
      @click="loadCharacters"
      color="#e92428"
      class="text-none"
      dark
      :disabled="loading"
      >Load Characters...</v-btn
    >
    <br />
    <span v-if="loading">Loading...</span>
    <v-progress-linear
      v-if="loading"
      indeterminate
      color="cyan"
    ></v-progress-linear>
    <v-item-group multiple>
      <v-container>
        <v-row>
          <v-col
            v-for="character in characters"
            :key="character.id"
            class="d-flex child-flex"
            cols="4"
          >
            <v-card class="mx-auto" max-width="370">
              <v-img
                class="align-end text-white"
                height="200"
                :src="
                  character.thumbnail.path + '.' + character.thumbnail.extension
                "
                cover
              >
                <v-card-title>
                  <v-btn color="yellow" @click="loadSumary(character.id)">
                    {{ character.name }}
                  </v-btn>
                </v-card-title>
              </v-img>

              <v-card-text>
                <div>
                  {{
                    character.description
                      ? character.description
                      : 'The description about this character is not founded or is empty. We are working to keep all information up to date'
                  }}
                </div>
              </v-card-text>

              <v-card-actions>
                <v-btn
                  color="#e92428"
                  class="text-none"
                  dark
                  @click="loadSumary(character.id)"
                >
                  Explore
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-item-group>
  </div>
</template>

<script lang="ts">
import axios from 'axios';
import Swal from 'sweetalert2';
export default {
  data() {
    return {
      characters: [],
      loading: false,
    }
  },
  methods: {
    loadCharacters() {
      console.log('Cargar Personajes')
      const ts = 21
      const hash = '78ca84cf14899fc8e9679108d1cffeab'
      const apikey = '8b29bc630dd53f8cbe2ddb63722ffff0'
      const num = getRandomInt(0, 1542)

      const url =
        'https://gateway.marvel.com:443/v1/public/characters?ts=' +
        ts +
        '&apikey=' +
        apikey +
        '&hash=' +
        hash +
        '&offset=' +
        num

      this.loading = true;
      axios
        .get(url)
        .then((response) => {
          const results = response.data.data.results;
          this.characters = results;
          console.log(results);
        })
        .catch((error) => {
          alert('Ha ocurrido un error al cargar los personajes');
          console.log(error);
        })
        .finally(() => {
          this.loading = false;
        })
      function getRandomInt(min: any, max: any) {
        min = Math.ceil(min);
        max = Math.floor(max);
        return Math.floor(Math.random() * (max - min) + min);
      }
    },
    loadSumary(id: any) {
      const defaultValue =
        'The description about this character is not founded or is empty. We are working to keep all information up to date'
      const found = this.characters.find((character) => character.id == id)
      for (const prop in found) {
        if (found[prop] == '') {
          found[prop] = defaultValue
        }
      }

      let summary =
        `
        <p>Description: ` +
        found.description +
        `</p><br>
        <p>Comics: ` +
        found.comics.available +
        `</p><br>
        <p>Series: ` +
        found.series.available +
        `</p><br>
        <p>Stories: ` +
        found.stories.available +
        `</p><br>
        <p>Events: ` +
        found.events.available +
        `</p><br>
        <p>Three Series about: `
      console.log(found.series.items.length)
      if (found.series.items.length < 3) {
        summary = summary + 'There are not enough series'
      } else {
        for (let i = 0; i < 3; i++) {
          const item = found.series.items[i]
          summary = summary + (i + 1) + ') ' + item.name + '- '
        }
      }

      Swal.fire({
        imageUrl: found.thumbnail.path + '.' + found.thumbnail.extension,
        imageWidth: 400,
        imageHeight: 250,
        title: found.name,
        html: summary,
        confirmButtonColor: "black",
        confirmButtonText:"Close"
      })
    },
  },
}
</script>

<style>
.swal2-popup {
  font-family: 'Roboto', sans-serif !important;
  background: linear-gradient(to bottom, #e92428, yellow) !important;
}
</style>