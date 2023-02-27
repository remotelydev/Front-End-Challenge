<template>
  <div>
    <div>
      <button
        class="prev-page"
        :disabled="currentPage === 1"
        @click="$emit('go-to-page', currentPage - 1)"
      >
        ‹
      </button>
      <template v-if="pages < 5">
        <button
          v-for="page in pages"
          :key="`page-${page + 1}`"
          type="button"
          :class="page === currentPage ? 'current' : ''"
          @click="$emit('go-to-page', page)"
        >
          {{ page }}
        </button>
      </template>
      <template v-else>
        <button
          v-for="(_, i) in 5"
          :key="`page-${i + pageOffset}`"
          type="button"
          :class="i + pageOffset === currentPage ? 'current' : ''"
          @click="$emit('go-to-page', i + pageOffset)"
        >
          {{ i + pageOffset }}
        </button>
      </template>
      <button
        class="next-page"
        :disabled="currentPage === pages"
        @click="$emit('go-to-page', currentPage + 1)"
      >
        ›
      </button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    total: {
      type: Number,
      default: 0
    },
    skip: {
      type: Number,
      default: 0
    },
    limit: {
      type: Number,
      default: 10
    }
  },
  emits: ['go-to-page'],
  computed: {
    pages() {
      return Math.floor(this.total / this.limit)
    },
    currentPage() {
      if (this.skip === 0) return 1

      return this.skip / this.limit + 1
    },
    start() {
      return this.currentPage - 2 || 1
    },
    end() {
      if (this.currentPage + 2 < this.pages) return this.currentPage + 2
      return this.pages
    },
    pageOffset() {
      const offset = this.currentPage - 2
      if (1 < offset) {
        if (offset + 4 < this.pages) return offset
        else return this.pages - 4
      }
      return 1
    }
  }
}
</script>

<style lang="scss" scoped>
button {
  width: 37px;
  height: 37px;
  background: none;
  border: 0;
  border-radius: 50%;
  font: normal normal normal 20px/27px Open Sans;

  &.current {
    background: var(--c-blue);
    color: var(--c-white);
  }
}
.prev-page,
.next-page {
  font: normal normal 600 30px/41px Open Sans;
}
</style>
