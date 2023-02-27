<template>
  <div class="card">
    <div class="thumbnail">
      <img :src="images[0]" alt="" />
    </div>
    <div class="details">
      <h4>{{ title }}</h4>
      <p>{{ description }}</p>
      <div class="price-n-rating">
        <div class="price">
          <span class="price-discounted">${{ discountedPrice.toFixed(2) }}</span>
          <span class="price-full">${{ price.toFixed(2) }}</span>
        </div>
        <SStars :rating="rating" />
      </div>
    </div>
  </div>
</template>

<script>
import SStars from '@/components/SStars.vue'

export default {
  components: {
    SStars
  },
  props: {
    title: {
      type: String,
      default: ''
    },
    description: {
      type: String,
      default: ''
    },
    price: {
      type: Number,
      default: 0
    },
    discountPercentage: {
      type: Number,
      default: 0
    },
    rating: {
      type: Number,
      default: 0
    },
    images: {
      type: Array,
      default: () => []
    }
  },
  computed: {
    discountedPrice() {
      return (this.price * (100 - this.discountPercentage)) / 100
    }
  }
}
</script>

<style lang="scss">
.card {
  width: 250px;
  height: 308px;
  border: 1px solid var(--c-gray-dark);
  border-radius: 5px;
  overflow: hidden;
}

.thumbnail {
  img {
    width: 250px;
    height: 141px;
    object-fit: cover;
  }
}

.details {
  padding: 0.5rem 1rem;

  h4 {
    width: 218px;
    overflow: hidden;
    font: normal normal bold 20px/26px Roboto;
    text-overflow: ellipsis;
    white-space: nowrap;
    margin-bottom: 0.5rem;
  }
  p {
    overflow: hidden;
    max-height: 44px;
    font: normal normal normal 16px/22px Open Sans;
    text-overflow: ellipsis;
    margin-bottom: 0.5rem;
  }
}

.price-n-rating {
  display: flex;
  justify-content: space-between;

  .price {
    display: flex;
    flex-direction: column;
  }
  .price-discounted {
    color: var(--c-blue);
    font: normal normal bold 20px/27px Open Sans;
  }
  .price-full {
    font: normal normal bold 14px/19px Open Sans;
    text-decoration: line-through;
  }
}
</style>
