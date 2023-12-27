<script setup lang="ts">
import { computed, ref, watch } from 'vue';
import sampleData from '../data/sample.json'

interface Item {
  id: number
  name: string
}


// import TheWelcome from '../components/TheWelcome.vue'
const items = ref<Item[]>([])
const searchTerm = ref("")

watch(searchTerm, () => {
  if (searchTerm.value === '') {
    items.value = []
  } else {
      setTimeout(() => {
        items.value = sampleData.filter(item => {
          return item.name.toLowerCase().indexOf(searchTerm.value) > -1
        })
      }, 300)
  }
})

const highlightedResults = computed(() => {
  return items.value.map(item => {
    const matches = [...item.name.toLowerCase().matchAll(new RegExp(searchTerm.value, 'g'))].map(match=> ({
      start: (match.index as number),
      end: (match.index as number) + searchTerm.value.length
    }))


    return {
      raw: item,
      highlight: {
        start: matches[0].start,
        end: matches[0].end
      }
    }
  })
})


</script>

<template>
  <main>
    <!-- <TheWelcome /> -->
    <input type="text" v-model.trim="searchTerm" />
    <button @click="searchTerm = ''">Clear</button>

    <ul v-if="items.length > 0">
      <li v-for="item of highlightedResults" :key="item.raw.id">
        {{ item.raw.name.slice(0, item.highlight.start) }}<b>{{ item.raw.name.slice(item.highlight.start, item.highlight.end) }}</b>{{ item.raw.name.slice(item.highlight.end) }}
      </li>
    </ul>
    <div v-else>No Results Found</div>
  </main>
</template>
