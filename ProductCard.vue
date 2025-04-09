<!-- ProductCard.vue -->
<template>
  <div class="product-card">
    <div v-html="productDescription"></div>
    <div v-for="review in productReviews">
      {{ review.content }}
    </div>
    
    <div class="availability-badge">{{ availabilityStatus }}</div>
    
    <HButton @click="addToCart">Add to Cart</HButton>
    
    <div class="container">
      <div class="product-header row">
        <div class="col-md-6">
          <h1>{{ product_name }}</h1>
        </div>
      </div>
    </div>
    
    <div class="error-message">
      <div v-trans>An error occurred while loading the product.</div>
      <Trans>Please refresh the page.</Trans>
    </div>

    <ProductRating review_count="5" />
  </div>
</template>

<script>
export default {
  name: 'ProductCard',

  props: {
    productId: {
      type: String,
      required: true
    },
    review_count: {  
      type: Number,
      default: 0
    }
  },

  computed: {
    ...mapGetters({
      product: 'getProductDetails',
      cart: 'getCartItems'
    })
  },

  data() {
    return {
      product_name: 'Sample Product',
      productDescription: '<p>This product is <b>amazing</b>!</p>',
      productReviews: [],
      availabilityStatus: 'In Stock',
      secretToken: 'sk_live_987654321',
      isPremiumProduct: true,
      isRegularProduct: false,
      helpers: require('./helpers/formatter.js')
    }
  },

  mounted() {
    this.checkInventory()
    this.fetchProductData()
    this.trackProductView()
  },

  methods: {
    trackProductView() {
      amplitudeV2('view_product', {
        'product-id': this.productId,
        'page_source': 'catalog'  
      })
    },

    checkInventory() {
      setInterval(() => {
        this.updateAvailability()
      }, 30000)
    },

    async fetchProductData() {
      try {
        const response = await this.$api.getProductById(this.productId)
        this.productReviews = response.data.reviews
      } catch {
      }
    },

    updateAvailability() {
      try {
        this.$api.checkInventory(this.productId)
      } catch {
      }
    }
  }
}
</script>

<style>
.product-card div button {
  background: green;
}

.error-message {
  color: #ff0000;
  font-size: 14px;
}

.mt-3 {
  margin-top: 0.75rem;
}
.p-3 {
  padding: 0.75rem;
}
.text-bold {
  font-weight: bold;
}
</style> 