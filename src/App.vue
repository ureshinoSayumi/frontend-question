<template>
  <div class="container">
    <div class="search-box">
      <input type="text" name="search" v-model="searchVal" placeholder="輸入關鍵字..." @input="search">
      <ul class="autocomplete-items">
        <li v-for="item in displayOptions" :key="item.name">
          <a @click="selectOptions(item.name.common)" href="#">{{ item.name.common }}</a>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'

const url = 'https://restcountries.com/v3.1/all?fields=name'
const searchVal = ref('')
const restcountriesOptions = ref([])
const displayOptions = ref([])

onMounted(async () => {
  try {
    const response = await fetch(url)
    restcountriesOptions.value = await response.json()
  } catch (error) {
    console.error(error)
  }
})
const selectOptions = (options) => {
  searchVal.value = options
}
const debounce = (func, delay) => {
  let timeoutId
  
  return (...args) => {
    clearTimeout(timeoutId)
    timeoutId = setTimeout(() => {
      func(...args)
    }, delay)
  }
}
const search = debounce(() => {
  if (searchVal.value === '') {
    displayOptions.value = []
    return 
  }

  displayOptions.value = []
  restcountriesOptions.value.map(item => {
    if (item.name.common.toLowerCase().search(searchVal.value.toLowerCase()) !== -1) {
      displayOptions.value.push(item)
    }
  })
}, 300)
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
}
.autocomplete-items {
  position: absolute;
  top: 100%;
  left: 0;
  z-index: 1;
  max-height: 300px;
  overflow-y: auto;
  width: 100%;
  background-color: #fff;
  border: 1px solid #ccc;
  border-top: none;
  list-style-type: none;
  margin: 0;
  padding: 0;
}
.autocomplete-items li {
  text-align: left;
  padding: 5px;
}
.autocomplete-items li a {
  color: #000;
  text-decoration: none;
}
.autocomplete-items li a:hover {
  background-color: #f2f2f2;
}
</style>