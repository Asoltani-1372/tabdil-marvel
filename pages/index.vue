<template>

    <div v-if="characters" class="backGroundColorHome min-h-screen">

        <div
            class="searchContainer h-40 custom-gradient content-center justify-self-center text text-center px-14 mt-10">
            <div class="img h-16 w-16 ml-20 mb-2">
                <img src="/assets/images/marvel.svg" alt="marvel">
            </div>
            <div
                class="px-4 py-2 h-18 rounded-md w-11/12 mx-auto mt- flex flex-row justify-between  searchinputOuter-bg">
                <input @keyup="onKeyUpSearch" v-model="searchQuery" type="text" placeholder="Search for character..."
                    class="w-11/12 searchinput-bg pl-2  text-white" />
                <button @click="onClick" type="submit"
                    class="text-white bg-red-500 hover:bg-red-700 py-2 px-4 rounded flex items-center justify-between">
                    <i class="bx bx-search 2xl text-2xl w-1/5 mr-2 text-white"></i>
                    search</button>
            </div>

            <ClientOnly>
                <div ref="searchResultContainer" v-if="searchResultcomp && showResults"
                    class="grid  grid-cols-1  md:grid-cols-2 lg:grid-cols-2 gap-6 px-20 h-32 mr-10 ml-10 mt-10 ">
                        <div  v-for="item in searchResultcomp" :key="item.id" class="relative group '">
                            <NuxtLink :to="`${item.id}`">
                                <div
                                    class="bg-gray-500 rounded-sm overflow-hidden hover:shadow-xl transition-shadow h-96 ">
                                    <img :src="`${item.thumbnail.path}.${item.thumbnail.extension}`" alt="Image"
                                        class="w-full h-full object-cover">
                                </div>
                                <div class="p-4">
                                    <h3 class="text-xs font-semibold  text-gray-100">{{ item.name }}</h3>
                                    <div
                                        class="absolute inset-0 bg-black bg-opacity-70 text-white p-4 flex items-center justify-center opacity-0 group-hover:opacity-100 transition-opacity">
                                        <p>{{ item.description || 'No description available' }}</p>
                                    </div>
                                </div>


                            </NuxtLink>
                        </div>
                </div>
            </ClientOnly>
        </div>
        <div>
            
        </div>

        <div v-if="!showResults" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 px-20 h-full mr-10 ml-10">
            <div v-for="item in characters.data.results" :key="item.id" class="relative group">
                <NuxtLink :to="`${item.id}`">
                    <HomeCharacterItem :item="item" />

                </NuxtLink>
            </div>
        </div>
        <div v-if="showPagination && !showResults" class="Paginationcontainer mx-auto pb-10 ">
            <div class="flex justify-center items-center mx-auto mt-4 space-x-2">
                <button @click="goToFirstPage" :disabled="currentPage === 1"
                    class="px-4 py-2 rounded-full bg-transparent  text-gray-200 disabled:opacity-50 disabled:cursor-not-allowed hover:bg-gray-700 hover:text-white">
                    <i class="bx bx-chevrons-left 2xl text-2xl pt-1"></i>
                </button>
                <button @click="goToPrevious" :disabled="currentPage === 1"
                    class="px-4 py-2 rounded-full bg-transparent  text-gray-200 disabled:opacity-50 disabled:cursor-not-allowed hover:bg-gray-700 hover:text-white">
                    <i class="bx bx-chevron-left 2xl text-2xl pt-1"></i>
                </button>
                <div class="flex space-x-2">
                    <button v-for="page in PageToShowComputed" :key="page" @click="goToPage(page)
                        "
                        :class="['px-4 py-2 rounded-full bg-transparent ', { 'bg-red-600 text-white': page === currentPage, 'text-gray-200 hover:bg-red-500 hover:text-white': page !== currentPage }]">
                        {{ page }}
                    </button>
                </div>
                <div v-if="!(PageToShowComputed.includes(Math.ceil(totalPage)))" class="text-white text-2x pb-2">
                    . . .
                </div>
                <button @click="goToNext"
                    :disabled="currentPage === currentPage === Math.floor(totalPage) || currentPage >= Math.ceil(totalPage)"
                    class="px-4 py-2 rounded-full bg-transparent  text-gray-200 disabled:opacity-50 disabled:cursor-not-allowed hover:bg-gray-700 hover:text-white">
                    <i class="bx bx-chevron-right 2xl text-2xl pt-1"></i>
                </button>
                <button @click="goToLastPage" :disabled="PageToShowComputed.includes(Math.ceil(totalPage))"
                    class="px-4 py-2 rounded-full bg-transparent  text-gray-200 disabled:opacity-50 disabled:cursor-not-allowed hover:bg-gray-700 hover:text-white">
                    <i class="bx bx-chevrons-right 2xl text-2xl pt-1"></i>
                </button>
            </div>
        </div>
    </div>
</template>

<script setup>

import CryptoJS from 'crypto-js'
// Variables
let currentPage = ref(1)
const searchQuery = ref('');
let showPagination = ref(true)
const ShowExistSearches = ref(true)
const showResults = ref(false)
const searchResult = reactive([])
const searchResultContainer = ref(null)


const publicKey = process.env.MARVEL_PUBLIC_KEY || "01b7cd1063a56ef1b5b7f22d39e41a97"
const privateKey = process.env.MARVEL_PRIVATE_KEY || "8bb180165fbcbe71e7c8cbe80293c0fd245e5fc2"

const ts = Date.now().toString()
const hash = CryptoJS.MD5(`${ts}${privateKey}${publicKey}`).toString()
const InitialUrl = `https://gateway.marvel.com/v1/public/characters?apikey=${publicKey}&ts=${ts}&hash=${hash}&limit=12}`

// Data Fetching
const { data: characters } = await useAsyncData(
    () => $fetch(InitialUrl, {
        query: { offset: (currentPage.value - 1) * 12 }
    }), {
    watch: [currentPage]
}
)
const getSearchData = async (url, search) => {
    if (search) {
        url = `${url}&nameStartsWith=${searchQuery.value}`
    }
    characters.value = await $fetch(url, {
    })
}

const onClick = () => {
    if (searchQuery && searchQuery.value.length > 0) {

        getSearchData(InitialUrl, "search")
        showPagination.value = false
        ShowExistSearches.value = true
    }
    else {
        getSearchData(InitialUrl)
        ShowExistSearches.value = false
        showPagination.value = true
    }
}

const onKeyUpSearch = async () => {
    console.log(searchQuery)
    if (searchQuery && searchQuery.value.length > 2) {
        searchResult.data = await  $fetch(InitialUrl, {
                query: { nameStartsWith: searchQuery.value, limit: 100 }
        })
           console.log(searchResult)
    }
    openSearchConatiner()
}





//Computed PRoperties

const searchResultcomp = computed(() => {
    console.log(searchResult , "searchResult")
    return searchResult?.data?.data?.results
})

const totalPage = computed(() => {
    return characters.value.data.total / 12
})
const PageToShowComputed = computed(() => {
    let initialNumber
    if (currentPage.value > Math.floor(totalPage.value - 5)) {
        initialNumber = Math.floor(totalPage.value - 3)

    }
    else {
        initialNumber = currentPage.value
    }
    const NewArr = [];
    for (let i = initialNumber || 1; i < initialNumber + 5; i++) {
        NewArr.push(i);
    }
    return NewArr
})

// Methodes

const goToPage = (page) => {
    if (page !== currentPage.value) {
        currentPage.value = page
    }
};
const goToFirstPage = (page) => {
    currentPage.value = 1
}

const goToPrevious = () => {
    if (currentPage.value > 1) {
        currentPage.value = currentPage.value - 1;
    }
};

const goToNext = () => {
    if (currentPage.value < totalPage.value) {
        currentPage.value = currentPage.value + 1
    }
};
const goToLastPage = () => {

    currentPage.value = Math.ceil(totalPage.value)
};

const openSearchConatiner = () => {
    if (searchQuery.value) {
        showResults.value = true
    };
}

function closeContainer() {
    showResults.value = false;
}

function handleClickOutside(event) {
    console.log(event , "event")
    if (searchResultContainer.value && !searchResultContainer.value.contains(event.target)) {
        closeContainer();
    }
}

onMounted(() => {
  document.addEventListener('click', handleClickOutside);
});

// Clean up event listeners before component unmounts
onBeforeUnmount(() => {
    document.removeEventListener('click', handleClickOutside);
});

</script>
<style>
.custom-gradient {
    --gradient-start: #2C2E30;
    --gradient-end: #000000;
    --gradient-fade: rgba(255, 0, 0, 0.15);
    /* Brighter red color with higher opacity */
    background:
        radial-gradient(circle at 15% center,
            var(--gradient-fade) 0%,
            transparent 7%),
        linear-gradient(to right,
            var(--gradient-start) 0%,
            var(--gradient-start) 20%,
            var(--gradient-end) 50%,
            var(--gradient-end) 100%);
}

.searchinput-bg {
    background-color: #3B3D3F;
}

.searchinputOuter-bg {
    background-color: #2C2E30;
}

.backGroundColorHome {
    background-color: #2C2E30;
}
</style>