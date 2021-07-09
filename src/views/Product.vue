<template>
  <div class="about">
    <Loading :active="isLoading"></Loading>
    <div class="modal-dialog modal-xl" role="document">
      <div class="modal-content border-0">
        <div class="modal-header bg-dark text-white">
          <h5 class="modal-title" id="exampleModalLabel">
            <span>{{ tempProduct.title }} </span>
          </h5>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="modal"
            aria-label="Close"
          ></button>
        </div>
        <div class="modal-body">
          <div class="row">
            <div class="col-sm-6">
              <img class="img-fluid" :src="tempProduct.imageUrl" alt="" />
            </div>
            <div class="col-sm-6">
              <span class="badge bg-primary rounded-pill">{{
                tempProduct.category
              }}</span>
              <p>商品描述：{{ tempProduct.description }}</p>
              <p>商品內容：{{ tempProduct.content }}</p>
              <del class="h6">原價 {{ tempProduct.origin_price }} 元</del>
              <div class="h5">現在只要 {{ tempProduct.price }} 元</div>
              <div>
                <div class="input-group">
                  <input
                    type="number"
                    class="form-control"
                    min="1"
                    v-model.number="qty"
                  />
                  <button
                    type="button"
                    class="btn btn-primary"
                    @click="addCart(tempProduct.id, qty)"
                  >
                    加入購物車
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Product',
  data() {
    return {
      tempProduct: {},
      qty:1,
      isLoading: false,
      loadingStatus: {
        loadingStatus: ''
      },
    };
  },
  methods:{
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
        } else {
          alert(res.data.message);
        }
      });
    },
    getProduct(){
      const { id } = this.$route.params;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/product/${id}`;
      this.$http.get(api).then((res) => {
        if(res.data.success) {
          const { product } = res.data;
          this.tempProduct = product;
        } else {
          alert(res.data.message);
        }
      });
    }
  },
  created() {
    this.getProduct();
  },
};
</script>