<template>
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
</template>


<script setup props="item">
const props = defineProps({
    item : {
        type : Object,
        required : true,
    }
 })

const {item} = props

</script>

<style>
</style>