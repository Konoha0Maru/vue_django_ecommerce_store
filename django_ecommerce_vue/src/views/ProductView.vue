<template>
  <nav class="breadcrumb" aria-label="breadcrumbs">
    <ul>
      <li><router-link to="/">Boutique</router-link></li>
      <li><a href="#">Products</a></li>
      <li class="is-active"><a href="#" aria-current="page">{{ product.name }}</a></li>
    </ul>
  </nav>

<div class="page-product">
  <div class="columns is-multiline">
    <div class="column is-half">
      <figure class="image mb-6">
        <img v-bind:src="product.get_image">
      </figure>
    </div>

    <div class="column is-one-quarter">
      <div class="information is my-6 pb-6">
        <h2 class="subtitle">Information</h2>

        <h1 class="title">{{ product.name }}</h1>

        <p>{{ product.description }}</p>
      </div>

      <p><strong>Price: </strong>${{ product.price }}</p>

      <div class="field has-addons mt-3">
        <div class="control">
          <input type="number" class="input" min="1" v-model="quantity">
        </div>

        <div class="control">
          <a class="button is-info" @click="addToCart()">Add to cart</a>
        </div>
      </div>
    </div>
  </div>
</div>
</template>

<script>
import axios from 'axios'
import { toast } from 'bulma-toast'

export default {
  name: "ProductView",
  data() {
    return {
      product: {},
      quantity: 1
    }
  },
  mounted() {
    this.getProduct()
  },
  methods: {
    async getProduct() {
      this.$store.commit('setIsLoading', true)

      const category_slug = this.$route.params.category_slug
      const product_slug = this.$route.params.product_slug

      await axios
          .get(`/api/v1/products/${category_slug}/${product_slug}`)
          .then(response => {
            this.product = response.data

            document.title = this.product.name + ' | Boutique'
          })
          .catch(error => {
            console.log(error)
          })

      this.$store.commit('setIsLoading', false)
    },
    addToCart() {
      if (isNaN(this.quantity) || this.quantity < 1) {
        this.quantity = 1
      }

      const item = {
        product: this.product,
        quantity: this.quantity
      }

      this.$store.commit('addToCart', item)

      toast({
        message: 'The product was added to cart',
        type: 'is-success',
        dismissible: true,
        pauseOnHover: true,
        duration: 2000,
        position: 'bottom-right',
      })
    }
  }
}
</script>

<style scoped>

</style>