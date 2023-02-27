<template>
  <main>
    <h1>Front End Challenge</h1>

    <form>
      <label class="search" for="search">
        <span>Search:</span>
        <input
          v-model="search"
          class="search-input"
          type="search"
          name="search"
          placeholder="What are you looking for?"
        />
      </label>

      <label class="category" for="category">
        <span>Category:</span>
        <select v-model="selectedCategory">
          <option :value="undefined">Select category</option>
          <option v-for="category in categories" :key="category" :value="category">
            {{ category }}
          </option>
        </select>
      </label>
    </form>

    <hr />

    <div class="sort-controls">
      <button
        :class="sort.includes('price') ? 'active' : ''"
        type="button"
        @click="setSort('price')"
      >
        price
        <span v-if="sort.includes('price')">{{ sort.includes('ascending') ? '▲' : '▼' }}</span>
      </button>
      <button
        :class="sort.includes('stock') ? 'active' : ''"
        type="button"
        @click="setSort('stock')"
      >
        stock
        <span>{{ sort.includes('stock') ? (sort.includes('ascending') ? '▲' : '▼') : '' }}</span>
      </button>
      <button
        :class="sort.includes('rating') ? 'active' : ''"
        type="button"
        @click="setSort('rating')"
      >
        rating
        <span>{{ sort.includes('rating') ? (sort.includes('ascending') ? '▲' : '▼') : '' }}</span>
      </button>
    </div>

    <div class="product-list">
      <SCard v-for="product in displayedProducts" :key="`product-${product.id}`" v-bind="product" />
    </div>

    <div class="bottom-controls">
      <select id="limit" v-model="limit" name="limit">
        <option v-for="limitValue in limits" :key="`limit-${limitValue}`" :value="limitValue">
          {{ limitValue }}
        </option>
      </select>
      <SPagination :total="total" :skip="skip" :limit="limit" @go-to-page="goToPage" />
      <div></div>
    </div>
  </main>
</template>

<script>
import SCard from '@/components/SCard.vue'
import SPagination from '@/components/SPagination.vue'

import { debounce } from './utils'

export default {
  components: {
    SCard,
    SPagination
  },
  data() {
    return {
      products: [],
      categories: [],
      selectedCategory: undefined,
      search: '',
      limit: 10,
      limits: [10, 25, 50, 100],
      total: 0,
      skip: 0,
      sort: ''
    }
  },
  computed: {
    displayedProducts() {
      if (this.sort.length === 0) return this.products

      const productsToSort = [...this.products]

      switch (this.sort) {
        case 'price-ascending':
          return productsToSort.sort((a, b) => {
            return a.price * (100 - a.discountPercentage) - b.price * (100 - b.discountPercentage)
          })
        case 'price-descending':
          return productsToSort.sort((a, b) => {
            return b.price * (100 - b.discountPercentage) - a.price * (100 - a.discountPercentage)
          })
        case 'stock-ascending':
          return productsToSort.sort((a, b) => {
            return a.stock - b.stock
          })
        case 'stock-descending':
          return productsToSort.sort((a, b) => {
            return b.stock - a.stock
          })
        case 'rating-ascending':
          return productsToSort.sort((a, b) => {
            return a.rating - b.rating
          })
        case 'rating-descending':
          return productsToSort.sort((a, b) => {
            return b.rating - a.rating
          })
      }

      return this.products
    },
    sortedProducts() {
      const productsToSort = this.products
      return productsToSort.sort((a, b) => {
        return a.price - b.price
      })
    }
  },
  watch: {
    search() {
      this.debouncedFetch(this.search)
    },
    limit() {
      this.getProducts(this.search)
    },
    selectedCategory() {
      this.getProductsOfCategory(this.selectedCategory)
    }
  },
  created() {
    this.debouncedFetch = debounce((phrase) => {
      this.getProducts(phrase)
    }, 1000)
  },
  mounted() {
    this.getProducts()
    this.getCategories()
  },
  methods: {
    debouncedSearch: debounce((arg) => {
      this.getProducts(arg)
    }, 1000),
    getProducts(phrase, page) {
      let endpoint = `https://dummyjson.com/products?limit=${this.limit}`

      if (phrase) {
        endpoint = `https://dummyjson.com/products/search?q=${phrase}&limit=${this.limit}`
        if (this.selectedCategory) this.selectedCategory = undefined
      }

      if (page) {
        endpoint += `&skip=${(page - 1) * this.limit}`
      }

      fetch(endpoint)
        .then((res) => res.json())
        .then((res) => {
          this.products = res.products
          this.total = res.total
          this.skip = res.skip
        })
    },
    getCategories() {
      fetch('https://dummyjson.com/products/categories')
        .then((res) => res.json())
        .then((res) => {
          this.categories = res
        })
    },
    getProductsOfCategory(category) {
      fetch(`https://dummyjson.com/products/category/${category}?limit=${this.limit}`)
        .then((res) => res.json())
        .then((res) => {
          this.products = res.products
        })
    },
    goToPage(page) {
      this.getProducts(this.search, page)
    },
    setSort(type) {
      if (!this.sort) {
        this.sort = `${type}-ascending`
      } else if (this.sort.includes('ascending')) {
        this.sort = `${type}-descending`
      } else {
        this.sort = ''
      }
    }
  }
}
</script>

<style lang="scss" scoped>
h1 {
  font: normal normal bold 20px/27px Open Sans;
  margin-bottom: 40px;
}

form {
  display: flex;
  gap: 30px;
}

hr {
  margin: 2rem 0 0.75rem;
}

.search {
  display: flex;
  flex-direction: column;
  font: normal normal 600 16px/22px Open Sans;
  .search-input {
    width: 288px;
    height: 40px;
    border: 1px solid var(--c-gray-dark);
    border-radius: 5px;
    padding: 0.5rem 1rem;
  }
}
.category {
  display: flex;
  flex-direction: column;
  font: normal normal 600 16px/22px Open Sans;

  select {
    height: 40px;
    border: 1px solid var(--c-gray-dark);
    border-radius: 5px;
    padding: 0.5rem 1rem;
  }
}

.sort-controls {
  display: flex;
  gap: 10px;
  margin-bottom: 40px;

  button {
    display: flex;
    align-items: center;
    background: var(--c-gray-light);
    border: 0;
    height: 30px;
    padding: 0.25rem 0.75rem;
    border-radius: 6px;
    font: normal normal normal 16px/22px Open Sans;

    &.active {
      background: var(--c-blue);
      color: var(--c-white);
    }
    span {
      font-size: 0.66rem;
      margin-left: 3px;
    }
  }
}
.product-list {
  display: flex;
  flex-wrap: wrap;
  gap: 2.75rem;
  width: 100%;
  margin-bottom: 40px;
}
.bottom-controls {
  display: flex;
  justify-content: space-between;

  #limit {
    height: 40px;
    display: flex;
    align-items: center;
    // padding: 0.5rem 0 0.5rem 1rem;
    padding-left: 1rem;
    font: normal normal normal 18px/24px Open Sans;
  }
}

@media (min-width: 1024px) {
}
main {
  max-width: 1752px;
  margin: 0 auto;
  padding: 50px 1rem;
}
</style>
