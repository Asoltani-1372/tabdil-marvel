<template>
    <div class="bg-gray-500 rounded-sm overflow-hidden hover:shadow-xl transition-shadow h-96 ">
        <img :src="`${CharacterSeriesItem.thumbnail.path}.${CharacterSeriesItem.thumbnail.extension}`" alt="Image"
            class="w-full h-full object-cover">
    </div>
    <div class="p-4">
        <h3 class="text-xs font-semibold  text-gray-100">{{ CharacterSeriesItem.title }}</h3>
    </div>

</template>


<script setup prop="CharacterSeriesItem">
const props = defineProps({
    CharacterSeriesItem : {
        type : Object,
        required : true,
    }
 })

const {CharacterSeriesItem} = props
</script>