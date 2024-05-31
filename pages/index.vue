<script setup>
import { ref, watch, onMounted } from 'vue';
import { useFetch } from '@vueuse/core';

const searchQuery = ref("star");
const resultQuery = ref([]);
const arrayData = ref([]);
const loader = ref(false);

const searchData = async () => {
    loader.value = true;
    const { data, isFetching, error } = await useFetch(`https://images-api.nasa.gov/search?q=${searchQuery.value}`).json();

    if (data.value) {
        resultQuery.value = data.value.collection.items;
        loader.value = false;
        console.log(data.value.collection);
    } else {
        console.error(error.value);
        loader.value = false;
    }
};

onMounted(async () => {
    await searchData();
});

watch(resultQuery, (newValue) => {
    arrayData.value = newValue;
    console.log(newValue); 
}, {
    deep: true 
});
</script>

<template>
    <div>
        <input type="text" v-model="searchQuery">
        <button @click="searchData" v-if="!loader">Wyszukaj</button>
        <button @click="searchData" disabled v-else>Wyszukaj</button>
        <div class="cos" v-if="loader">Ładowanie danych...</div>

        <div class="result" v-else>
            <div v-for="(item, index) in arrayData" :key="index">
                <img v-if="item.links && item.links[0].href" :src="item.links[0].href" :alt="item.data[0].title" />
                <p v-if="item.data && item.data[0].title">{{ item.data[0].title }}</p>
            </div>
        </div>
    </div>
</template>

<style scoped lang="scss">
.cos {
    font-size: 1.5em;
    color: blue;
}

.result {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
}

.result div {
    width: 200px;
    text-align: center;
}

.result img {
    max-width: 100%;
    height: auto;
}
</style>
