<template>
  <div>
    <h1>This is 產品列表頁面</h1>
    <Loading :active="isLoading"></Loading>
    <table class="table align-middle">
      <thead>
        <tr>
          <th>圖片</th>
          <th>商品名稱</th>
          <th>價格</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in products" :key="item.id">
          <td style="width: 200px">
            <div
              style="
                height: 100px;
                background-size: cover;
                background-position: center;
              "
              :style="{ backgroundImage: `url(${item.imageUrl})` }"
            ></div>
          </td>
          <td>
            {{ item.title }}
          </td>
          <td>
            <div class="h5" v-if="!item.price">{{ item.origin_price }} 元</div>
            <del class="h6" v-if="item.price"
              >原價 {{ item.origin_price }} 元</del
            >
            <div class="h5" v-if="item.price">現在只要 {{ item.price }} 元</div>
          </td>
          <td>
            <div class="btn-group btn-group-sm">
                <router-link
                  class="btn btn-outline-secondary"
                  :to="`/product/${item.id}`"
                  >查看更多</router-link
                >
              <button
                type="button"
                class="btn btn-outline-danger"
                @click="addCart(item.id)"
                :disabled="loadingStatus.loadingItem === item.id"
              >
                <i
                  class="fas fa-spinner fa-pulse"
                  v-if="loadingStatus.loadingItem === item.id"
                ></i>
                加到購物車
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>

export default {
  name: 'Products',
  data() {
    return {
      //all products
      products: [],
      //loading
      loadingStatus: {
        loadingStatus: ''
      },
      isLoading: false,
      // product
      product: {},
    };
  },
  created() {
    this.getProductList();
  },
  methods: {
    addCart(id, qty = 1) {
      this.isLoading= true;
      this.loadingStatus.loadingStatus = id; 
      //define the cart structure     
      const cart = {
        product_id: id,
        qty,
      };
      const url = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`;
      this.$http.post(url, { data: cart }).then((res) => {
        if(res.data.success) {
          alert(res.data.message);
          this.loadingStatus.loadingStatus = '';
          // 分頁處理
          // this.getCart(); 
          this.isLoading= false;
          this.$refs.userProductModal.hideModal();
        } else {
          alert(res.data.message);
        }
      });
    },
    getProductList() {
      this.isLoading= true;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/products`;
      this.$http.get(api).then((res) => {
          if (res.data.success) {
            const {products} = res.data;
            this.products = products;
            this.isLoading= false;
            // console.log(this.products);
          } else {
            alert(res.data.message);
          }
        });
    },
    getProduct(item) {
      this.isLoading= true;
      this.loadingStatus.loadingStatus = item.id;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/product/${item.id}`;
      this.$http.get(api).then((res) => {
        if(res.data.success) {
          const { product } = res.data;
          this.loadingStatus.loadingStatus = '';
          this.product = product;
          this.isLoading= false;
          this.$refs.userProductModal.openModal();
        } else {
          alert(res.data.message);
        }
      });
    },

  }
};
</script>