<template>
  <div>
    <div class="filter_wrapper">
      <select
        class="select"
        v-model="selectedCategory"
        @change="changeCategory"
      >
        <option
          v-for="option in filterCategory"
          :key="option.value"
          :value="option.value"
        >
          {{ option.name }}
        </option>
      </select>
      <select class="select" v-model="selectedMethod" @change="changeMethod">
        <option
          v-for="option in filterMethod"
          :key="option.value"
          :value="option.value"
        >
          {{ option.name }}
        </option>
      </select>
      <input
        class="search"
        type="text"
        v-model.trim="searchQuery"
        placeholder="Search..."
      />
    </div>
    <div class="pagination_wrapper">
      <div
        class="page"
        :class="{ current_page: num === pageNumber }"
        v-for="(num, idx) in totalPages"
        :key="idx"
        @click="changePage(num)"
      >
        {{ num }}
      </div>
    </div>
    <table>
      <caption>
        Supply list
      </caption>

      <colgroup>
        <col />
        <col />
        <col />
        <col />
      </colgroup>
      <tr>
        <th>Shipment date</th>
        <th @click="sortedBy = 'organizationName'">Organization name</th>
        <th @click="sortedBy = 'containerQuantity'">Number of containers</th>
        <th @click="sortedBy = 'distance'">Distance to warehouse</th>
      </tr>
      <tr
        v-for="(supply, idx) in sortedSearchedAndPaginatedSupplies"
        :key="idx"
      >
        <td>{{ supply.date }}</td>
        <td>{{ supply.organizationName }}</td>
        <td>{{ supply.containerQuantity }}</td>
        <td>{{ supply.distance }}</td>
      </tr>
    </table>
  </div>
</template>

<script>
import { mapState } from "vuex";
export default {
  data: () => {
    return {
      sortedBy: "",
      searchQuery: "",
      selectedCategory: "",
      filterCategory: [
        { value: "organizationName", name: "Organization" },
        { value: "containerQuantity", name: "Containers" },
        { value: "distance", name: "Distance" },
      ],
      selectedMethod: "",
      filterMethod: [
        { value: "equal", name: "equal to" },
        { value: "contain", name: "contains" },
        { value: "more", name: "more than" },
        { value: "less", name: "less than" },
      ],
      pageNumber: 1,
      pageLimit: 10,
    };
  },
  methods: {
    changeCategory(event) {
      this.selectedCategory = event.target.value;
    },
    changeMethod(event) {
      this.selectedMethod = event.target.value;
    },
    changePage(num) {
      this.pageNumber = num;
    },
  },
  computed: {
    ...mapState({
      supplies: (state) => state.supplies,
    }),
    sortedSupplies() {
      const array = [...this.supplies];
      array.sort((supply1, supply2) => {
        return `${supply1[this.sortedBy]}`?.localeCompare(
          `${supply2[this.sortedBy]}`,
          "all",
          { numeric: true }
        );
      });
      return array;
    },
    sortedAndSearchedSupplies() {
      return this.sortedSupplies.filter((supply) => {
        const actualValue = `${supply[this.selectedCategory]}`;
        const searchedValue = `${this.searchQuery}`;
        if (this.searchQuery.length !== 0) {
          switch (this.selectedMethod) {
            case "equal":
              return (
                actualValue.toLowerCase().replace(/[.,&]/g, "") ==
                searchedValue.toLowerCase().replace(/[.,&]/g, "")
              );
            case "contain":
              return actualValue
                .toLowerCase()
                .includes(searchedValue.toLowerCase());
            case "more":
              return +actualValue > +searchedValue;
            case "less":
              return parseFloat(actualValue) < parseFloat(searchedValue);
            default:
              return supply;
          }
        } else return supply;
      });
    },
    sortedSearchedAndPaginatedSupplies() {
      const start = (this.pageNumber - 1) * this.pageLimit;
      const end = start + this.pageLimit;
      return this.sortedAndSearchedSupplies.slice(start, end);
    },
    totalPages() {
      const total = this.sortedAndSearchedSupplies.length;
      const limit = this.pageLimit;
      return Math.ceil(total / limit);
    },
  },
  watch: {
    totalPages: {
      handler(newVal) {
        if (newVal < this.pageNumber) {
          this.changePage(newVal);
        }
      },
    },
    pageNumber: {
      handler(newVal) {
        if (newVal < 1) {
          this.changePage(1);
        }
      },
    },
  },
};
</script>

<style lang="scss" scoped>
table {
  width: 100%;
  border: 1px solid black;
  border-collapse: collapse;
  caption {
    margin: 0 0 20px;
    font-weight: 700;
    font-size: 25px;
  }
  col {
    width: 25%;
  }
  tr {
    td,
    th {
      border: 1px solid black;
      padding: 15px 5px 15px 5px;
    }
    th {
      background-color: rgb(228, 250, 130);
      user-select: none;
    }
    td {
      background-color: rgb(249, 255, 227);
    }
  }
}
.filter_wrapper {
  display: flex;
  height: 30px;
  justify-content: center;
  .select {
    margin: 0 2px 0 2px;
  }
  .search {
    margin: 0 2px 0 2px;
  }
}
.pagination_wrapper {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  margin: 10px 0 10px 0;
  .page {
    border: 2px solid black;
    width: 20px;
    height: 20px;
    font-weight: 700;
    padding: 10px;
    margin: 0 2px 0 2px;
    border-radius: 5px;
    cursor: pointer;
    user-select: none;
  }
  .current_page {
    border: 2px solid rgb(54, 229, 241);
    padding: 10px;
    cursor: pointer;
  }
}
</style>