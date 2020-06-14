<template>
  <div class="container">
    <div class="row">
      <div class="col-sm-10">
        <h1>Callorie adder</h1>
        <hr><br><br>
        <button type="button" class="btn btn-success btn-sm">Add Product</button>
        <br><br>
        <v-toolbar
            dark
            color="teal"
          >
            <v-toolbar-title>Product selection</v-toolbar-title>
            <v-autocomplete
              v-model="select"
              :loading="loading"
              :items="items"
              :search-input.sync="search"
              cache-items
              class="mx-4"
              flat
              hide-no-data
              hide-details
              label="Choose your product"
              solo-inverted
            ></v-autocomplete>
            <div class="my-2">
                <v-btn outlined color="grey lighten-2" @click="addCurrentProduct" dark large> Add </v-btn>
            </div>
        </v-toolbar>
        <table class="table table-hover">
          <thead>
            <tr>
              <th scope="col">Product name</th>
              <th scope="col">Callorie</th>
              <th scope="col">Product weight</th>
              <th scope="col">Callorie summary</th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(currentProduct, index) in currentProducts" :key="index">
              <td>{{ currentProduct.name }}</td>
              <td>{{ currentProduct.callorie }}</td>
              <td><input v-model="currentProduct.weight" placeholder="Product weight"> </td>
              <td>{{ currentProduct.weight ? (currentProduct.weight * currentProduct.callorie) / 100 : "" }}</td>
              <td>
                <button type="button" class="btn btn-danger btn-sm" @click="deleteCurrentProduct(currentProduct)">Delete</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>


<script>
import axios from 'axios';

export default {
  name: 'Adder',
  data() {
    return {
      products: '',
      loading: false,
      items: [],
      search: null,
      select: null,
      currentProducts: {},
    };
  },
  watch: {
    search (val) {
      val && val !== this.select && this.querySelections(val)
    },
  },
  methods: {
    getProducts() {
      const path = 'http://localhost:5000/products';
      axios.get(path)
        .then((res) => {
          this.products = res.data.products;
        })
        .catch((error) => {
          // eslint-выключение следующей строки
          console.error(error);
        });
    },
    getCurrentProducts() {
      const path = 'http://localhost:5000/current_products';
      axios.get(path)
        .then((res) => {
          this.currentProducts = res.data.current_products;
        })
        .catch((error) => {
          // eslint-выключение следующей строки
          console.error(error);
        });
    },
    deleteCurrentProduct(currentProduct) {
      const path = `http://localhost:5000/current_products/${currentProduct.id}`;
      axios.delete(path)
        .then(() => {
          this.getCurrentProducts()
        })
        .catch((error) => {
          // eslint-выключение следующей строки
          console.error(error);
        });
    },
    initSelectedProduct() {
        this.loading = false
        this.items = []
        this.search = null
        this.select = null
    },
    addCurrentProduct() {
        const path = 'http://localhost:5000/current_products';
        const payload = {
            name: this.selectedProduct.name,
            callorie: this.selectedProduct.callorie
        }
        axios.post(path, payload)
            .then(()=> {
                this.getCurrentProducts();
                }
            )
            .catch(error => {
                console.error(error);
                this.getCurrentProducts();
            })
         this.initSelectedProduct();
    },
    querySelections (char) {
      this.loading = true
      // Simulated ajax query
      setTimeout(() => {
        this.items = this.products.reduce((acc, x) =>
            x.name.toLowerCase().indexOf(char.toLowerCase()) > -1 ?  [...acc, x.name] : acc, [])
        this.loading = false
      }, 500)
    },
  },
  computed: {
    selectedProduct: function() {
            return this.products.filter(x =>
                (x.name || '').toLowerCase().indexOf((this.select || '').toLowerCase()) > -1 ?  x : false)[0];
            }
    },

  created() {
    this.getProducts();
  },
};
</script>