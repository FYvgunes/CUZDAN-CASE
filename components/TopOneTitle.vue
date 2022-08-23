<template>
  <div class="topOneTitle">
    <h2>Find the most common words in an array</h2>
    <ol>
      <li v-for="item in topWord" :key="item">{{ item }}</li>
    </ol>
  </div>
</template>
<script>
export default {
    name: "TopOneTitles",
    
    props:{
        topWord: { 
            type: Array,
            default: []
        }
    }
}
</script>