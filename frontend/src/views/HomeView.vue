<script>
export default {
  data() {
    return {
      products: [],
      loading: true,
      error: false,
      filterValue: ''
    };
  },
  mounted() {
    this.fetchProducts();
  },
  methods: {
    fetchProducts() {
      fetch('http://localhost:3000/api/products')
        .then(response => {
          if (!response.ok) {
            throw new Error('Network response was not ok');
          }
          return response.text();
        })
        .then(data => {
          if (data) {
            this.products = JSON.parse(data);
          }
          this.loading = false;
        })
        .catch(error => {
          console.error(error);
          this.loading = false;
          this.error = true;
        });
    },
    filterProducts() {
      return this.products.filter(product => {
        return product.name.toLowerCase().includes(this.filterValue.toLowerCase());
      });
    }
  }
};
</script>


<template>
  <div>
    <h1 class="text-center mb-4">Liste des produits</h1>

    <div class="row mb-3">
      <div class="col-md-6">
        <form>
          <div class="input-group">
            <span class="input-group-text">Filtrage</span>
            <input
              type="text"
              class="form-control"
              placeholder="Filtrer par nom"
              data-test-filter
              v-model="filterValue"
            />
          </div>
        </form>
      </div>
      <div class="col-md-6 text-end">
        <div class="btn-group">
          <button
            type="button"
            class="btn btn-primary dropdown-toggle"
            data-bs-toggle="dropdown"
            aria-expanded="false"
            data-test-sorter
          >
            Trier par nom
          </button>
          <ul class="dropdown-menu dropdown-menu-end">
            <li>
              <a class="dropdown-item" href="#"> Nom </a>
            </li>
            <li>
              <a class="dropdown-item" href="#" data-test-sorter-price>
                Prix
              </a>
            </li>
          </ul>
        </div>
      </div>
    </div>

    <div class="text-center mt-4" data-test-loading>
      <div class="spinner-border" role="status">
        <span class="visually-hidden">Chargement...</span>
      </div>
    </div>

    <div class="alert alert-danger mt-4" role="alert" data-test-error>
      Une erreur est survenue lors du chargement des produits.
    </div>
    <div class="row">
      <div class="col-md-4 mb-4" v-for="product in filterProducts()" :key="product.id" data-test-product>
        <div class="card">
          <RouterLink :to="{ name: 'Product', params: { productId: product.id } }">
            <img
              :src="product.picture"
              data-test-product-picture
              class="card-img-top"
            />
          </RouterLink>
          <div class="card-body">
            <img :src="product.pictureUrl" data-test-product-picture class="card-img-top" />

            <h5 class="card-title">
              <RouterLink
                data-test-product-name
                :to="{ name: 'Product', params: { productId: product.id } }"
              >
                {{ product.name }}
              </RouterLink>
            </h5>
            <p class="card-text" data-test-product-description>
              {{ product.description }}
            </p>
            <p class="card-text">
              Vendeur :
              <RouterLink
                data-test-product-seller
                :to="{ name: 'User', params: { userId: product.sellerId } }"
              >
                {{ product.seller }}
              </RouterLink>
            </p>
            <p class="card-text" data-test-product-date>
              En cours jusqu'au {{ product.endDate }}
            </p>
            <p class="card-text" data-test-product-price>Prix actuel : {{ product.price }} €</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
      