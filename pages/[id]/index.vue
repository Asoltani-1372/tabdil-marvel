<template>

    <!-- Add additional elements here if needed, like a button or icon -->
    <div v-if="character" class="bg-gray-800 min-h-screen">
        // Hero
        <CharacterDetails :CharacterDetails="character" />
        // CharacterComic

        <div class="comicContainer mt-3">
            <div class="h-32">
                <div class="h-20"></div>
                <h1 class="text-white text-xl  ml-8">Comics</h1>
            </div>
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 h-full mr-10 ml-10">
                <div v-for="comic in characterComics.data.results" :key="comic.id" class="relative group">
                    <CharacterComicsItem :caracterComicsItems="comic" />

                </div>
            </div>
        </div>
        // CharacterSeries
        <div v-if="characterSeries" class="bg-gray-800">

            <div class="seriesContainer">
                <div class="h-32">
                    <div class="h-20"></div>
                    <h1 class="text-white text-xl  ml-8">Series</h1>
                </div>
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 h-full mr-10 ml-10">
                    <div v-for="series in characterSeries.data.results" :key="series.id" class="relative group">
                        <CharacterSeriesItem :CharacterSeriesItem="series" />
                    </div>
                </div>
            </div>
        </div>
    </div>

</template>
<script setup>
const route = useRoute()
import { useFetch } from '#app';
import CryptoJS from 'crypto-js'

const publicKey = process.env.MARVEL_PUBLIC_KEY || "01b7cd1063a56ef1b5b7f22d39e41a97"
const privateKey = process.env.MARVEL_PRIVATE_KEY || "8bb180165fbcbe71e7c8cbe80293c0fd245e5fc2"
const ts = Date.now().toString()
const hash = CryptoJS.MD5(`${ts}${privateKey}${publicKey}`).toString()
const urlcharacter = `https://gateway.marvel.com/v1/public/characters/${route.params.id}?apikey=${publicKey}&ts=${ts}&hash=${hash}`
const urlCommic = `https://gateway.marvel.com/v1/public/characters/${route.params.id}/comics?apikey=${publicKey}&ts=${ts}&hash=${hash}`
const urlSeries = `https://gateway.marvel.com/v1/public/characters/${route.params.id}/series?apikey=${publicKey}&ts=${ts}&hash=${hash}`



const { data: character } = await useFetch(urlcharacter, {
    onRequestError({ request, options, error }) {
        console.log(error, request, options)
    },
});
const { data: characterComics } = await useFetch(urlCommic, {
    onRequestError({ request, options, error }) {
        console.log(error, request, options)
    },
});
const { data: characterSeries } = await useFetch(urlSeries, {
    onRequestError({ request, options, error }) {
        console.log(error, request, options)
    },
});

</script>
  
  <style scoped>

.custom-gradient {
  --gradient-start: #2C2E30;
  --gradient-end: #000000;
  --gradient-fade: rgba(255, 0, 0, 0.2); /* Brighter red color with higher opacity */
  background: 
    radial-gradient(circle at 15% center, 
      var(--gradient-fade) 0%, 
      transparent 10%
    ),
    linear-gradient(to right, 
      var(--gradient-start) 0%, 
      var(--gradient-start) 20%, 
      var(--gradient-end) 50%, 
      var(--gradient-end) 100%
    );
}
</style>