<template>
    <div class="flex h-16 w-16 ml-10">
                <img src="/assets/images/marvel.svg" alt="marvel">
        </div>
    <div class="characterContainer flex ml-10 pt-10 pb-10 custom-gradient">
        
        <div class=" w-1/6 characterImgContainer ">
            <img :src="`${CharacterDetails.data.results[0].thumbnail.path}.${CharacterDetails.data.results[0].thumbnail.extension}`"
                alt="Image" class="h-64 w-64 object-cover">

        </div>
        <div class=" w-4/5 DetailsContainer m-auto">
            <h1 class="text-gray-100 text-2xl mb-2">{{ CharacterDetails.data.results[0].name }}</h1>
            <h6 class="text-gray-200">{{ CharacterDetails.data.results[0].description || 'No description available' }}
            </h6>
            <div class="heroBtnContainer flex ">
                <button
                    class="bg-transparent text-gray-400 mr-1 rounded-md hover:text-gray-700 px1 p-2 mt-2 flex  justify-center border border-white">
                    <i class="bx bx-right-top-arrow-circle mr-2"></i> Detail
                </button>
                <button
                    class="bg-transparent text-gray-400 mx-1 rounded-md hover:text-gray-600 px1 p-2 mt-2 flex justify-center border border-white">
                    <i class="bx bx-right-top-arrow-circle mr-2"></i> Wiki
                </button>
                <button
                    class="bg-transparent text-gray-400 mx-1 rounded-md hover:text-gray-700 px1 p-2 mt-2 flex  justify-center border border-white">
                    <i class="bx bx-right-top-arrow-circle mr-2"></i> Comiclink
                </button>
            </div>
        </div>
    </div >    
</template>

<script setup prop="CharacterDetails" >
    const props = defineProps( {
        CharacterDetails : {
            type : Object,
            require : true
        }
    })

    const {CharacterDetails} = props

</script>