<template>

    <!-- Add additional elements here if needed, like a button or icon -->
    <div v-if="character" class="bg-gray-800" >
        <div class="characterContainer flex ml-10 pt-10 pb-10 custom-gradient">
            <div class=" w-1/6 characterImgContainer ">
                <img :src="`${character.data.results[0].thumbnail.path}.${character.data.results[0].thumbnail.extension}`"
                    alt="Image" class="h-64 w-64 object-cover">

            </div>
            <div class=" w-4/5 characterDetailsContainer m-auto">
                <h1 class="text-gray-100 text-2xl mb-2">{{ character.data.results[0].name }}</h1>
                <h6 class="text-gray-200">{{ character.data.results[0].description || 'No description available' }}</h6>
                <div class="heroBtnContainer flex ">
                <button
                    class="bg-transparent text-gray-500 mr-1 rounded-md hover:text-gray-700 px1 p-2 mt-2 flex  justify-center border border-white">
                    <i class="bx bx-right-top-arrow-circle mr-2"></i> Detail
                </button>
                <button
                    class="bg-transparent text-gray-500 mx-1 rounded-md hover:text-gray-700 px1 p-2 mt-2 flex justify-center border border-white">
                    <i class="bx bx-right-top-arrow-circle mr-2"></i> Wiki
                </button>
                <button
                    class="bg-transparent text-gray-500 mx-1 rounded-md hover:text-gray-700 px1 p-2 mt-2 flex  justify-center border border-white">
                    <i class="bx bx-right-top-arrow-circle mr-2"></i> Comiclink
                </button>
            </div>
            </div>


        </div>
        <div class="comicContainer mt-3">
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 h-full mr-10 ml-10">
                <div v-for="comic in characterComics.data.results" :key="comic.id" class="relative group">
                    <div class="bg-gray-500 rounded-sm overflow-hidden hover:shadow-xl transition-shadow h-96 ">
                        <img :src="`${comic.thumbnail.path}.${comic.thumbnail.extension}`" alt="Image"
                            class="w-full h-full object-cover">
                    </div>
                    <div class="p-4">
                        <h3 class="text-xs font-semibold  text-gray-100">{{ comic.title }}</h3>
                    </div>

                </div>
            </div>
        </div>
    </div>
    <div v-if="characterSeries" class="bg-gray-800">

        <div class="seriesContainer">
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 h-full mr-10 ml-10">
                <div v-for="comic in characterSeries.data.results" :key="comic.id" class="relative group">
                    <div class="bg-gray-500 rounded-sm overflow-hidden hover:shadow-xl transition-shadow h-96 ">
                        <img :src="`${comic.thumbnail.path}.${comic.thumbnail.extension}`" alt="Image"
                            class="w-full h-full object-cover">
                    </div>
                    <div class="p-4">
                        <h3 class="text-xs font-semibold  text-gray-100">{{ comic.title }}</h3>
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
  console.log(process.env.MARVEL_PUBLIC_KEY , "%55555555555")
  const ts = Date.now().toString()
  const hash = CryptoJS.MD5(`${ts}${privateKey}${publicKey}`).toString()
  console.log(route , "route")
    const urlcharacter = `https://gateway.marvel.com/v1/public/characters/${route.params.id}?apikey=${publicKey}&ts=${ts}&hash=${hash}`
    const urlCommic = `https://gateway.marvel.com/v1/public/characters/${route.params.id}/comics?apikey=${publicKey}&ts=${ts}&hash=${hash}`
    const urlSeries = `https://gateway.marvel.com/v1/public/characters/${route.params.id}/series?apikey=${publicKey}&ts=${ts}&hash=${hash}`



const { data :character } = await useFetch(urlcharacter, {
  onRequestError({ request, options, error }) {
     console.log(error , request , options)
  },
});
const { data :characterComics } = await useFetch(urlCommic, {
  onRequestError({ request, options, error }) {
     console.log(error , request , options)
  },
});
const { data :characterSeries } = await useFetch(urlSeries, {
  onRequestError({ request, options, error }) {
     console.log(error , request , options)
  },
});
console.log(character , "33333")


  </script>
  
  <style scoped>
  .custom-gradient {
    --gradient-start: #2d3748;
    --gradient-end: #000000;
    background: linear-gradient(to right, var(--gradient-start) 0%, var(--gradient-end) 70%, var(--gradient-end) 100%);
  }
</style>