<template>
     <div class="karma">
        <h2>
          Titles with at least 9,000 karma points by the author in ten articles
        </h2>
        <ul>
          <li v-for="item in titleListForKarma" :key="item">{{ item }}</li>
        </ul>
      </div>
</template>
<script>
export default {
    props: {
        titleListForKarma: {
            type: Array,
            default: []
        }
    }
}
</script>