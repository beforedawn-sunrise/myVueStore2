<template>
    <div>
        <loading :active.sync="isLoading"></loading>
        <div class="text-right mt-4">
            <button class="btn btn-primary" v-on:click="openModal(true)">建立新的產品</button>
            <table class="table mt-4">
                <thead>
                    <tr>
                        <th width="120">分類</th>
                        <th width="140">產品名稱</th>
                        <th width="120">原價</th>
                        <th width="120">售價</th>
                        <th width="100">是否啟用</th>
                        <th width="80">編輯</th>
                        <th width="80">刪除</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(item) in products" :key="item.id">
                        <td>{{ item.category}}</td>
                        <td>{{ item.title}}</td>
                        <td class="text-right">
                            {{ item.origin_price | currency }}
                        </td>
                        <td class="text-right">
                            {{ item.price | currency }}
                        </td>

                        <td>
                            <span v-if="item.is_enabled" class="text-success">啟用</span>
                            <span v-else>未啟用</span>
                        </td>

                        <td>
                            <button class="btn btn-outline-primary btn-sm" v-on:click="openModal(false, item)">編輯</button>
                        </td>
                        <td>
                          <button class="btn btn-outline-danger btn-sm" v-on:click="deleteModal(item)">刪除</button>
                        </td>
                    </tr>
                </tbody>
            </table>
            
            <nav aria-label="Page navigation example">
              <ul class="pagination">
                <li class="page-item" :class="{'disabled': !pagination.has_pre}">
                  <a class="page-link" href="#" aria-label="Previous" @click.prevent="getProducts(pagination.current_page - 1)">
                    <span aria-hidden="true">&laquo;</span>
                  </a>
                </li>
                <li class="page-item" v-for="page in pagination.total_pages" :key="page" :class="{'active': pagination.current_page === page}">
                  <a class="page-link" href="#" @click.prevent="getProducts(page)">{{page}}</a>
                </li>
                <li class="page-item" :class="{'disabled': !pagination.has_next}">
                  <a class="page-link" href="#" aria-label="Next"  @click.prevent="getProducts(pagination.current_page + 1)">
                    <span aria-hidden="true">&raquo;</span>
                  </a>
                </li>
              </ul>
            </nav>
            <!-- Modal -->
            <div class="modal fade" id="productModal" tabindex="-1" role="dialog"
  aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog modal-lg" role="document">
    <div class="modal-content border-0">
      <div class="modal-header bg-dark text-white">
        <h5 class="modal-title" id="exampleModalLabel">
          <span>新增產品</span>
        </h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body">
        <div class="row">
          <div class="col-sm-4">
            <div class="form-group">
              <label for="image">輸入圖片網址</label>
              <input type="text" class="form-control" id="image"
                placeholder="請輸入圖片連結" v-model="tempProduct.imageUrl">
            </div>
            <div class="form-group">
              <label for="customFile">或 上傳圖片
                <i class="fa-solid fa-circle-notch fa-spin" v-if="status.fileUploading" style="font-family:'Font Awesome 5 Free';font-weight: 900"></i>
              </label>
              <input type="file" id="customFile" class="form-control"
                ref="files" @change="uploadFile">
            </div>
            <!--<img img="https://images.unsplash.com/photo-1483985988355-763728e1935b?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=828346ed697837ce808cae68d3ddc3cf&auto=format&fit=crop&w=1350&q=80"
              class="img-fluid" alt="" :src="tempProduct.imageUrl"> -->
            <img class="img-fluid" :src="tempProduct.imageUrl" alt="">
          </div>
          <div class="col-sm-8">
            <div class="form-group">
              <label for="title">標題</label>
              <input type="text" class="form-control" id="title"
                placeholder="請輸入標題" v-model="tempProduct.title">
            </div>

            <div class="form-row">
              <div class="form-group col-md-6">
                <label for="category">分類</label>
                <input type="text" class="form-control" id="category"
                  placeholder="請輸入分類" v-model="tempProduct.category">
              </div>
              <div class="form-group col-md-6">
                <label for="price">單位</label>
                <input type="unit" class="form-control" id="unit"
                  placeholder="請輸入單位" v-model="tempProduct.unit">
              </div>
            </div>

            <div class="form-row">
              <div class="form-group col-md-6">
              <label for="origin_price">原價</label>
                <input type="number" class="form-control" id="origin_price"
                  placeholder="請輸入原價" v-model="tempProduct.origin_price">
              </div>
              <div class="form-group col-md-6">
                <label for="price">售價</label>
                <input type="number" class="form-control" id="price"
                  placeholder="請輸入售價" v-model="tempProduct.price">
              </div>
            </div>
            <hr>

            <div class="form-group">
              <label for="description">產品描述</label>
              <textarea type="text" class="form-control" id="description"
                placeholder="請輸入產品描述" v-model="tempProduct.description"></textarea>
            </div>
            <div class="form-group">
              <label for="content">說明內容</label>
              <textarea type="text" class="form-control" id="content"
                placeholder="請輸入產品說明內容" v-model="tempProduct.content"></textarea>
            </div>
            <div class="form-group">
              <div class="form-check">
                <input class="form-check-input" type="checkbox"
                  id="is_enabled" v-model="tempProduct.is_enabled" :true-value="1" :false-value="0"> 
                <label class="form-check-label" for="is_enabled">
                  是否啟用
                </label>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-outline-secondary" data-dismiss="modal">取消</button>
        <button type="button" class="btn btn-primary" v-on:click="updateProduct">確認</button>
      </div>
    </div>
  </div>
</div>
<div class="modal fade" id="delProductModal" tabindex="-1" role="dialog"
  aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog" role="document">
    <div class="modal-content border-0">
      <div class="modal-header bg-danger text-white">
        <h5 class="modal-title" id="exampleModalLabel">
          <span>刪除產品</span>
        </h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body">
        是否刪除 <strong class="text-danger">{{ tempProduct.title }}</strong> 商品(刪除後將無法恢復)。
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-outline-secondary" data-dismiss="modal">取消</button>
        <button type="button" class="btn btn-danger" @click="deleteProduct"
          >確認刪除</button>
      </div>
    </div>
  </div>
</div>
        </div>
    </div>
</template>

<script>
import $ from 'jquery'

export default {
    data(){
        return {
            products:[],
            tempProduct:{},
            isNew: false,
            isLoading: false,
            status:{
              fileUploading: false,
            },
            pagination:{},

        }
    },
    methods:{
        getProducts(page = 1){
            const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/admin/products?page=${page}`; // 'http://localhost:3000/api/casper/products';
            const vm = this;

            vm.isLoading = true;

             /* 從api取得資料，然後... */
            this.$http.get(api).then((response) => {
              console.log(response.data);

              // 如果取得資料成功,就...
              if (response.data.success) {
                vm.isLoading = false;
                 /* 取得產品資料 */
                vm.products = response.data.products;
                 /* 取得所在分頁 */
                vm.pagination = response.data.pagination;
              }
            });
        },

        openModal(isNew,item){
            
            if(isNew){
              // 編輯產品衣料為空
              this.tempProduct = {};
              this.isNew = true;
            }else{
              console.log(item);

              // 取得編輯單一產品資料
              this.tempProduct = Object.assign({},item);
              this.isNew = false;
            }
            $('#productModal').modal('show');
        },
        updateProduct(){
          let api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/admin/product`; // 'http://localhost:3000/api/casper/products';
          let httpMethod = 'post';
          const vm = this;
          // 如果是編輯狀態,請使用此api
          if (!vm.isNew) {
            api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/admin/product/${vm.tempProduct.id}`;
            httpMethod = 'put';
          }
          console.log(process.env.APIPATH, process.env.CUSTOMPATH);

          /* 送出資料，然後... */
          this.$http[httpMethod](api, { data: vm.tempProduct }).then((response) => {
            console.log(response.data);

             // 如果送出資料成功,就...
            if (response.data.success) {
              $('#productModal').modal('hide');
              vm.getProducts();
            } else {
              $('#productModal').modal('hide');
              vm.getProducts();
              console.log('新增失敗');
            }
        // vm.products = response.data.products;
          });
        },

        deleteModal(item){
          console.log(item);
          // 取得單一產品資料
          this.tempProduct = Object.assign({},item);
          // this.tempProduct = item;
          $('#delProductModal').modal('show');
        },
        
        deleteProduct(){
          const vm = this;
          console.log(vm);
          const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/admin/product/${vm.tempProduct.id}`; // 'http://localhost:3000/api/casper/products';
          vm.isloading = true;

           /* 從api刪除資料，然後... */
          this.$http.delete(api).then((response) => {
            $('#delProductModal').modal('hide');//隱藏刪除視窗
            vm.isloading = false;
            vm.getProducts();//重新取得產品資料
          });
        },
        uploadFile() {
          console.log(this);
          // 取得圖片
          const uploadedFile = this.$refs.files.files[0];
          const vm = this;
          // 利用formData物件來模擬表單上傳
          const formData = new FormData();
          // 將圖片欄位新增進去
          formData.append('file-to-upload', uploadedFile);
          const url = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/admin/upload`;
          vm.status.fileUploading =true;
          this.$http.post(url, formData, {
            // 將表單格式改成formData格式
            headers: {
              'Content-Type': 'multipart/form-data',
            },
          }).then((response) => {
            console.log(response.data);

            // 如果取得資料成功,就...
            if (response.data.success) {
             
              // console.log(vm.tempProduct);
              vm.status.fileUploading =false;
              // 取得圖片網址
              vm.$set(vm.tempProduct, 'imageUrl', response.data.imageUrl);
               // vm.tempProduct.imageUrl = response.data.imageUrl;
            }else{
              // 顯示錯誤訊息
              this.$bus.$emit('messsage:push', response.data.message, 'danger');
            }
          });
        },

    },
    created(){
      this.getProducts();
    //  在一開始時,取得產品資料
    },


}
</script>