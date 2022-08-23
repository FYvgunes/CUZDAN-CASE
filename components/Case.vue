<!-- Please remove this file from your project -->
<template>
  <div>
    <Header></Header>
    <div class="container">
      <TopOneTitle :topWord="topWord"></TopOneTitle>
      <Karma :titleListForKarma="titleListForKarma"></Karma>
    </div>
  </div>
</template>

<script>
import TopOneTitle from './TopOneTitle.vue';
import Karma from './Karma.vue';
export default {
    name: "Case",
    data: () => ({
        lastTenItem: [],
        TopTenWordsList: [],
        getTitleList: [],
        topWord: [],
        karmaList: [],
        karmaTopTitle: [],
        titleListForKarma: [],
    }),
    components: { TopOneTitle, Karma },

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
            }, 3500);
        },

        async getDatalan() {
            let allString = null;
            this.getTitleList.forEach((item) => {
                allString += item.title;
            });
            this.TopTenWordsList = allString.split(" ");
            this.getKarmaList();
            this.topWord = this.mostFrequent(this.TopTenWordsList, 10);
        },

        mostFrequent(arr = [], num = 1) {
            const map = {};
            let keys = [];
            for (let index = 0; index < arr.length; index++) {
                if (map[arr[index]]) {
                    map[arr[index]]++;
                }
                else {
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
                    }
                    else {
                        return -1;
                    }
                }
                else {
                    return map[b] - map[a];
                }
            })
                .slice(0, num);
            return keys;
        },

        async getKarmaList() {
            this.getTitleList.forEach((item) => {
                this.$axios
                    .get(`/user/${item.by}.json`, { params: { print: "pretty" } })
                    .then((response) => {
                    this.karmaList.push(response.data);
                    this.karmaList = this.karmaList.filter((item) => item.karma > 9000);
                });
            });
            window.setTimeout(() => {
                this.getKarmaListTopTitles();
            }, 1000);
        },
        
        getKarmaListTopTitles() {
            let karmaTopTitleList = [];
            let karmaTopTitle = [];
            let allString = null;
            for (let i = 0; i < this.karmaList.length; i++) {
                this.getTitleList.forEach((item) => {
                    if (item.by == this.karmaList[i].id) {
                        karmaTopTitleList.push(item.title);
                    }
                });
            }
            karmaTopTitleList.forEach((item) => {
                allString += item;
            });
            karmaTopTitle = allString.split(" ");
            this.titleListForKarma = this.mostFrequent(karmaTopTitle, 10);
        },
    }
};
</script>
<style lang="scss">
.container {
  background-color: black;
  width: 100%;
  height: 100vh;
  padding: 1rem;
  display: flex;

  .topOneTitle {
     color: #fff;
    width: 50%;

    h2 {
      font-size: 2rem;
    }

    & ol {
      list-style: none;
      padding: 0.6em;

      li {
        font-size: 1.5rem;
      }
    }
  }

  .karma {
   color: #fff;
    width: 50%;

    h2 {
      font-size: 2rem;
    }

    & ul {
      list-style: none;
      padding: 0.6em;

      li {
        font-size: 1.5rem;
      }
    }
  }
}
</style>
