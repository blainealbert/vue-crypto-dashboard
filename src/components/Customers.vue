<template>
  <div id="customers-component">
    <nav id="customers-filter">
      <input
        type="text"
        placeholder="Search Customers"
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
    <div class="customers">
      <div v-show="searchResults !== null && searchResults.length === 0">
        <h2>No customer found with the search criteria</h2>
      </div>
      <div
        class="customers__card"
        v-for="customer in searchResults"
        v-show="searchResults !== null && searchResults.length > 0"
        :key="customer.id"
      >
        <div class="customers__header">
          <div class="button-group">
            <button class="button button--primary">View</button>
            <button class="button button--secondary">Edit</button>
          </div>
          <img
            class="customers__img"
            src=".././assets/customers/user1.jpg"
            :alt="customer.name"
          />
        </div>
        <div class="customers__content">
          <h3 class="customers__name">{{ customer.name }}</h3>
          <p class="customers__customername">@{{ customer.username }}</p>
          <p class="customers__catchphrase">
            "{{ customer.company.catchPhrase }}"
          </p>
          <p class="customers__email">
            <img
              class="icon icon-mail"
              src=".././assets/icons/PNG/icon-mail.png"
              alt="address"
            />
            {{ customer.email }}
          </p>
          <p class="customers__address">
            <img
              class="icon icon-map"
              src=".././assets/icons/PNG/icon-map.png"
              alt="map"
            />
            {{ customer.address.street }} {{ customer.address.suite }},
            {{ customer.address.city }}, {{ customer.address.zipcode }},
            {{ customer.address.geo.lat }}
            {{ customer.address.geo.lng }}
          </p>
          <p class="customers__phone">
            <img
              class="icon icon-phone"
              src=".././assets/icons/PNG/icon-phone.png"
              alt="phone"
            />
            {{ customer.phone }}
          </p>
          <p class="customers__website">
            <img
              class="icon icon-website"
              src=".././assets/icons/PNG/icon-website.png"
              alt="website"
            />
            {{ customer.website }}
          </p>
          <p class="customers__company-name">
            <img
              class="icon icon-company"
              src=".././assets/icons/PNG/icon-company.png"
              alt="company"
            />
            {{ customer.company.name }}
          </p>
          <p class="customers__company-bs">
            <img
              class="icon icon-industry"
              src=".././assets/icons/PNG/icon-industry.png"
              alt="industry"
            />
            {{ customer.company.bs }}
          </p>
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
    customerList: null,
    searchInput: null,
    sortResults: null,
    filterSelect: { id: 0, value: "none", text: "Select a Filter" },
    filterOptions: [
      { id: 0, value: "none", text: "Select a Filter" },
      { id: 1, value: "alphabetical", text: "Filter by Name (A-Z)" },
      { id: 2, value: "alphabetical-reverse", text: "Filter by Name (Z-A)" },
    ],
  }),
  mounted() {
    axios
      .get("https://jsonplaceholder.typicode.com/users")
      .then((response) => {
        //console.log(response);
        this.customerList = response.data;
      })
      .catch((error) => {
        console.log("error: ", error);
      });
  },
  computed: {
    sortedAZ() {
      let sortResults = this.customerList;
      function compare(a, b) {
        if (a.name < b.name) return -1;
        if (a.name > b.name) return 1;
        return 0;
      }
      return sortResults.sort(compare);
    },
    sortedZA() {
      let sortResults = this.customerList;
      function compare(a, b) {
        if (a.name > b.name) return -1;
        if (a.name < b.name) return 1;
        return 0;
      }
      return sortResults.sort(compare);
    },
    sortedNum() {
      let sortedResults = this.customerList;
      sortedResults = sortedResults.sort((a, b) => {
        return a.id - b.id;
      });
      return sortedResults;
    },
    searchResults() {
      if (this.searchInput) {
        return this.customerList.filter((item) => {
          return this.searchInput
            .toLowerCase()
            .split(" ")
            .every((s) => item.name.toLowerCase().includes(s));
        });
      } else if (this.filterSelect.value === "alphabetical") {
        return this.sortedAZ;
      } else if (this.filterSelect.value === "alphabetical-reverse") {
        return this.sortedZA;
      } else if (this.customerList !== null) {
        return this.sortedNum; //sorted by ID
      } else {
        return this.customerList;
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
.customers__card {
  border-radius: 10px;
  background-color: #fff;
  text-align: left;
  margin-bottom: 40px;
}
.customers__img {
  width: 100%;
  height: auto;
}
.customers__content {
  padding: 20px;
}
.customers__name {
  margin-bottom: 0;
  margin-top: 0;
}
.customers__customername {
  margin-top: 0;
}
.customers__catchphrase {
  color: #51C5FF;
}
.customers__header {
  position: relative;
}
.button-group {
  position: absolute;
  display: flex;
  width: 100%;
  height: calc(75vw - 40px);
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
#customers-filter {
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
  .customers {
    display: flex;
    flex-wrap: wrap;
    padding: 20px;
    justify-content: center;
  }
  .customers__card {
    width: 300px;
    margin: 20px;
  }
  #customers-filter {
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
