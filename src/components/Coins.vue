<template>
  <div id="coins-component">
    <nav id="coins-filter">
      <input
        type="text"
        placeholder="Search coins"
        class="searchbar"
        v-model="searchInput"
        :style="{
          'background-image':
            'url(' + require('./../assets/icons/PNG/icon-search.png') + ')',
        }"
        @input="searchUpdate"
      />
      <select
        class="filter-select"
        v-model="filterSelect"
        @change="filterUpdate"
      >
        <option
          v-for="filterOption in filterOptions"
          :value="filterOption"
          :key="filterOption.id"
        >
          {{ filterOption.text }}
        </option>
      </select>
    </nav>
    <div class="coins">
      <div v-show="searchResults !== null && searchResults.length === 0">
        <h2>No coin found with the search criteria</h2>
      </div>
      <div
        class="coins__card"
        v-for="coin in searchResults"
        v-show="searchResults !== null && searchResults.length > 0"
        :key="coin.id"
      >
        <div class="coins__header" :style="`background-image: url('${coin.logo_url}');`">
          <div class="button-group">
            <button class="button button--primary">View</button>
          </div>
        </div>
        <div class="coins__content">
          <h3>{{ coin.name }}</h3>
          <p>Price: ${{ coin.price }} CAD</p>

        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

require("./../assets/icons/PNG/icon-search.png"); //needed this for code sandbox to show search bg url

export default {
  data: () => ({
    coinList: null,
    searchInput: null,
    sortResults: null,
    filterSelect: { id: 0, value: "none", text: "Select a Filter" },
    filterOptions: [
      { id: 0, value: "none", text: "Select a Filter" },
      { id: 1, value: "price-asc", text: "Filter by Price (Ascending)" },
      { id: 2, value: "price-desc", text: "Filter by Price (Descending)" },
      { id: 1, value: "alphabetical", text: "Filter by Name (A-Z)" },
      { id: 2, value: "alphabetical-reverse", text: "Filter by Name (Z-A)" },
    ],
  }),
  mounted() {
    axios
      .get(`https://api.nomics.com/v1/currencies/ticker?key=b2452684f71278bbad765a157d9379ee&ids=BTC,ETH,DASH,DOGE,ADA,XLM,THETA&interval=1d,30d&convert=CAD&per-page=100&page=1`)
      .then((response) => {
        console.log(response);
        this.coinList = response.data;
      })
      .catch((error) => {
        console.log("error: ", error);
      });
  },
  computed: {
    sortedAZ() {
      let sortResults = this.coinList;
      function compare(a, b) {
        if (a.name < b.name) return -1;
        if (a.name > b.name) return 1;
        return 0;
      }
      return sortResults.sort(compare);
    },
    sortedZA() {
      let sortResults = this.coinList;
      function compare(a, b) {
        if (a.name > b.name) return -1;
        if (a.name < b.name) return 1;
        return 0;
      }
      return sortResults.sort(compare);
    },
    sortedPriceDesc() {
      let sortedResults = this.coinList;
      sortedResults = sortedResults.sort((a, b) => {
        return b.price - a.price;
      });
      return sortedResults;
    },
    sortedPriceAsc() {
      let sortedResults = this.coinList;
      sortedResults = sortedResults.sort((a, b) => {
        return a.price - b.price;
      });
      return sortedResults;
    },
    searchResults() {
      if (this.searchInput) {
        return this.coinList.filter((item) => {
          return this.searchInput
            .toLowerCase()
            .split(" ")
            .every((s) => item.name.toLowerCase().includes(s));
        });
      } else if (this.filterSelect.value === "alphabetical") {
        return this.sortedAZ;
      } else if (this.filterSelect.value === "alphabetical-reverse") {
        return this.sortedZA;
        } else if (this.filterSelect.value === "price-asc") {
        return this.sortedPriceAsc; //sorted by Price
      } else if (this.filterSelect.value === "price-desc" || this.coinList !== null) {
        return this.sortedPriceDesc; //sorted by Price
      } else {
        return this.coinList;
      }
    },
  },
  methods: {
    searchUpdate() {
      //console.log(this.searchInput); // logs the input value
    },
    filterUpdate() {
      // console.log(this.filterSelect.text); //logs the select text
      // console.log(this.filterSelect.value); //logs the select value
    },
  },
};
</script>

<style scoped>
#coins-component{
  padding: 40px;
}
.coins__card {
  border-radius: 10px;
  background-color: #fff;
  text-align: left;
  margin-bottom: 40px;
}
.coins__img {
  width: 100%;
  height: auto;
}
.coins__content {
  padding: 20px;
}
.coins__name {
  margin-bottom: 0;
  margin-top: 0;
}
.coins__header {
  position: relative;
  background-position: center;
  background-size: contain;
  background-repeat: no-repeat;
  width: 100%;
  height: 250px;
  margin-top: 20px;
}
.button-group {
  position: relative;
  display: flex;
  width: 100%;
  height: 100%;
  justify-content: center;
  align-items: center;
}
.button-group:hover .button {
  opacity: 1;
  pointer-events: all;
}
.button-group .button {
  opacity: 0;
  pointer-events: none;
  padding: 10px 15px;
  margin: 5px;
  font-weight: bolder;
  border-radius: 5px;
  font-size: 16px;
}
.button--primary {
  background-color: #fff;
  color: #1575BF;
}
.button--secondary {
  background-color: #1575BF;
  color: #fff;
}
.icon {
  width: 20px;
  height: auto;
  margin-right: 10px;
}
.icon.icon-map {
  width: 18px;
  margin-left: 1px;
  margin-right: 11px;
}
#coins-filter {
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  margin-bottom: 20px;
}
.filter-select {
  width: 250px;
  margin-left: auto;
  padding: 10px;
  border-radius: 5px;
  margin-bottom: 10px;
}
.searchbar {
  width: calc(100% - 55px);
  max-width: 100%;
  padding: 10px 10px 10px 40px;
  border-radius: 5px;
  background-position: left 10px center;
  background-size: 20px 20px;
  background-repeat: no-repeat;
  margin-bottom: 10px;
}
@media screen and (min-width: 768px) {
  .coins {
    display: flex;
    flex-wrap: wrap;
    padding: 20px;
    justify-content: center;
  }
  .coins__card {
    width: 300px;
    margin: 20px;
  }
  #coins-filter {
    flex-direction: row;
  }
  .searchbar {
    width: 450px;
  }
  .button-group {
    height: 225px;
  }
}
</style>
