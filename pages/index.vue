<template>

    <div v-if="characters" class="bg-gray-800">
        <div class="searchContainer">
            <input @keyup="onSearch" @click="openSearchConatiner" v-model="searchQuery" type="text" placeholder="Search..."
                class="px-4 py-2 border rounded-md w-full" />
        <ClientOnly>
            <div ref="searchResultContainer" v-if="searchResultcomp && showResults" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-6 gap-6 px-20 h-full mr-10 ml-10 mt-10">
                <div v-for="item in searchResultcomp" :key="item.id" class="relative group '">
                    <NuxtLink :to="`${item.id}`">
                        <div class="bg-gray-500 rounded-sm overflow-hidden hover:shadow-xl transition-shadow h-full w-full  ">
                            <img :src="`${item.thumbnail.path}.${item.thumbnail.extension}`" alt="Image"
                                class="w-full h-full object-cover">
                        </div>
                        <div class="p-4 h-full w-full">
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

        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 px-20 h-full mr-10 ml-10">
            <div v-for="item in characters.data.results" :key="item.id" class="relative group">
                <NuxtLink :to="`${item.id}`">
                    <div class="bg-gray-500 rounded-sm overflow-hidden hover:shadow-xl transition-shadow h-96 ">
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
        <div class="Paginationcontainer mx-auto pb-10 ">
            <div class="flex justify-center items-center mx-auto mt-4 space-x-2">
                <button @click="goToPrevious" :disabled="currentPage === 1"
                    class="px-4 py-2 rounded-full bg-transparent  text-gray-200 disabled:opacity-50 disabled:cursor-not-allowed hover:bg-gray-700 hover:text-white">
                    <i class="bx bx-chevron-left 2xl text-2xl"></i>
                </button>
                <div class="flex space-x-2">
                    <button v-for="page in totalPages" :key="page" @click="goToPage(page)
"
                        :class="['px-4 py-2 rounded-full bg-transparent ', { 'bg-red-700 text-white': page  === currentPage, 'text-gray-200 hover:bg-red-500 hover:text-white': page !== currentPage }]">
                        {{ page }}
                    </button>
                </div>
                <button @click="goToNext" :disabled="currentPage === totalPages"
                    class="px-4 py-2 rounded-full bg-transparent  text-gray-200 disabled:opacity-50 disabled:cursor-not-allowed hover:bg-gray-700 hover:text-white">
                    <i class="bx bx-chevron-right 2xl text-2xl"></i>
                </button>
            </div>
        </div>
    </div>
</template>

<script setup>

import CryptoJS from 'crypto-js' 
let currentPage = ref(1)
let totalPages = ref(10)
const searchQuery = ref('');
const showResults = ref(true);
const searchResult = reactive([])
const searchResultContainer = ref(null)

const publicKey = process.env.MARVEL_PUBLIC_KEY || "01b7cd1063a56ef1b5b7f22d39e41a97"
const privateKey = process.env.MARVEL_PRIVATE_KEY || "8bb180165fbcbe71e7c8cbe80293c0fd245e5fc2"
console.log(publicKey , privateKey, "333333333333")

const ts = Date.now().toString()
const hash = CryptoJS.MD5(`${ts}${privateKey}${publicKey}`).toString()
const url = `https://gateway.marvel.com/v1/public/characters?apikey=${publicKey}&ts=${ts}&hash=${hash}&limit=12}`
console.log(url)

const { data: characters } = await useAsyncData(
    () => $fetch(url, {
        query: { offset: (currentPage.value -1) * 12 }
    }), {
    watch: [currentPage]
}
)
console.log(characters , "characters")


const onSearch = async () => {
    console.log(searchQuery)
    if (searchQuery && searchQuery.value.length > 2) {
        searchResult.data = await  $fetch(url, {
                query: { nameStartsWith: searchQuery.value, limit: 100 }
            })
           console.log(searchResult)
    }
}



const searchResultcomp = computed(() => {
    return searchResult?.data?.data?.results
})


const goToPage = (page)
 => {
    if (page !== currentPage.value) {
        currentPage.value = page
        console.log(currentPage)
    }
};

const goToPrevious = () => {
    if (currentPage.value > 1) {
        currentPage.value = currentPage.value - 1;
    }
};

const goToNext = () => {
    if (currentPage.value < totalPages.value) {
        currentPage.value = currentPage.value + 1
    }
};

function openSearchConatiner() {
  if (searchQuery.value) {
    showResults.value = true
  } ;
}

function closeContainer() {
    console.log(showResults , "showResults")
  showResults.value = false;
}

function handleClickOutside(event) {
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
.pagination {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-top: 20px;
}

.pagination button {
    padding: 8px 16px;
    margin: 0 4px;
    border: 1px solid #ccc;
    background-color: #f0f0f0;
    cursor: pointer;
}

.pagination button:hover {
    background-color: #e0e0e0;
}

.pagination .page-numbers {
    display: flex;
}

.pagination .page-numbers button.active {
    background-color: #007bff;
    color: white;
    border-color: #007bff;
}
</style>