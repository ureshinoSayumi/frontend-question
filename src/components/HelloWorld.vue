<template>
  <div class="container">
    <div class="search-box">
      <input type="text" name="search" v-model="searchVal" placeholder="輸入搜索詞...">
      <ul class="autocomplete-items">
        <li v-for="item in displayOptions" :key="item.name">
          {{ item.name.common }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'

const searchVal = ref('')
const restcountriesOptions = ref()
const displayOptions = computed({
  get() {
    if (searchVal.value === '') {
      return []
    }
    let NewItems = []
    restcountriesOptions.value.map((item) => {
      if (item.name.common.toLowerCase().search(searchVal.value.toLowerCase()) !== -1) {
        NewItems.push(item);
      }
    });
    return NewItems;
  }
})

onMounted(async () => {
  try {
    const response = await fetch('https://restcountries.com/v3.1/all?fields=name')
    const data = await response.json()
    restcountriesOptions.value = data
  } catch (error) {
    console.error(error)
  }
})
</script>

<style scoped>
.container {
  display: flex;
  justify-content: center;    
  align-items: center;
  
  height: 100vh;
  width: 100%;
}
.search-box {
  position: relative;
  display: inline-block;
}

.autocomplete-items {
  position: absolute;
  top: 100%;
  left: 0;
  z-index: 1;
  max-height: 500px;
  overflow-y: auto;
  width: 100%;
  background-color: #fff;
  border: 1px solid #ccc;
  border-top: none;
  list-style-type: none;
  margin: 0;
  padding: 0;
}


.autocomplete-items li a {
  color: #000;
  text-decoration: none;
}

.autocomplete-items li a:hover {
  background-color: #f2f2f2;
}
</style>