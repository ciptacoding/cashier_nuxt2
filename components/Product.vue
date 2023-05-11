<template>
  <v-row class="flex-lg-column">
    <v-row align="center">
      <v-col cols="10">
        <v-autocomplete
          label="Products"
          placeholder="Start typing to search"
          :search-input.sync="search"
          :loading="isLoading"
          :items="itemsSearch"
          item-text="title"
          item-value="id"
          v-model="selectedSearch"
          return-object
			 hide-no-data
        >
        </v-autocomplete>
      </v-col>
      <v-col cols="2" class="d-flex justify-center">
        <v-menu>
          <template v-slot:activator="{ on: category }">
            <v-btn v-on="category" color="primary">Category</v-btn>
          </template>

          <v-list>
            <v-list-item-group v-model="categoryId">
              <v-list-item
                v-for="category in categories"
                :key="category.id"
                :value="category.id"
                :disabled="category.id == categoryId"
              >
                <v-list-item-title>{{ category.title }}</v-list-item-title>
              </v-list-item>
            </v-list-item-group>
          </v-list>
        </v-menu>
      </v-col>
    </v-row>

    <v-row>
      <v-col cols="2" v-for="(product, index) in filteredProducts" :key="index">
        <v-card :title="product.title" :ripple="true">
          <v-card-actions>
            <v-img
              :src="require(`@/assets/images/products/${product.thumbnail}`)"
            ></v-img>
          </v-card-actions>
          <v-card-text align="center" class="product-title">
            {{ product.title }}
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-row>
</template>

<script>
import {mapState} from 'vuex';

export default {
   data() {
      return {
         categoryId: false,
         categories: [
         { id: false, title: 'All' },
         { id: 1, title: 'Smartphone' },
         { id: 2, title: 'Camera' },
         { id: 3, title: 'Televisi' },
         ],
         
         search: null,
         selectedSearch: null,
         isLoading: false,
         itemsSearch: [],
      }
   },

   methods: {
      resetSearchCategory() {
         this.categoryId = false
      },
   },

   computed: {
      filteredProducts() {
         if (this.categoryId) {
				return this.products.filter(
					(item) => item.categoryId == this.categoryId
				)
         } else if (this.selectedSearch) {
				return this.products.filter(
					(item) => item.title == this.selectedSearch.title
				)
         }
         return this.products
      },
		...mapState('products', {
			products: 'products'
		})
   },

   watch: {
      search(val) {
         this.isLoading = true
         setTimeout(() => {
         	this.itemsSearch = this.products.filter((item) => {
					this.resetSearchCategory()
					this.isLoading = false
					return item.title
        		})
         }, 1000)
      },
   },
}
</script>

<style scoped>
.product-title {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
</style>
