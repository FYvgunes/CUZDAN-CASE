<!-- Please remove this file from your project -->
<template>
  <div>
    <Header></Header>
    <div>
      <h2>find the most common words in an array</h2>
      <ul>
        <li v-for="item in topWord" :key="item">{{ item }}</li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "Case",

  data: () => ({
    lastTenItem: [],
    TopTenWordsList: [],
    getTitleList: [],
    topWord: [],
    karmaList: [],
  }),

  mounted() {
    this.getTopStories();
  },

  methods: {
    async getTopStories() {
      const response = await this.$axios.get(`/newstories.json`, {
        params: {
          print: "pretty",
        },
      });
      this.lastTenItem = response.data.slice(0, 25);
      this.getTopTenWordsList();
    },

    async getTopTenWordsList() {
      this.lastTenItem.forEach((id) => {
        this.$axios.get(`/item/${id}.json`).then((response) => {
          this.getTitleList.push(response.data);
        });
      });
      window.setTimeout(() => {
        this.getDatalan();
      }, 5000);
    },

    async getDatalan() {
      let allString = null;
      this.getTitleList.forEach((item) => {
        allString += item.title;
      });
      this.TopTenWordsList = allString.split(" ");

      const mostFrequent = (arr = [], num = 1) => {
        const map = {};
        let keys = [];

        for (let index = 0; index < arr.length; index++) {
          if (map[arr[index]]) {
            map[arr[index]]++;
          } else {
            map[arr[index]] = 1;
          }
        }

        for (let item in map) {
          keys.push(item);
        }

        keys = keys
          .sort((a, b) => {
            if (map[a] === map[b]) {
              if (a > b) {
                return 1;
              } else {
                return -1;
              }
            } else {
              return map[b] - map[a];
            }
          })
          .slice(0, num);

        return keys;
      };

      this.getKarmaList();
      this.topWord = mostFrequent(this.TopTenWordsList, 10);
    },

    async getKarmaList() {
      this.getTitleList.forEach((item) => {
        this.$axios
          .get(`/user/${item.by}.json`, { params: { print: "pretty" } })
          .then((response) => {
            this.karmaList.push(response.data);
          });
      });
    },
  },
};
</script>
