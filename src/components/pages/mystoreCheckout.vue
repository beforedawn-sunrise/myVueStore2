<template>
<div>
    <mystoreNavbars></mystoreNavbars>
    <loading :active.sync="isLoading"></loading>
    <div class="container">
        <div class="row allSpace">
            <form class="col-md-6" @submit.prevent="payOrder">
                <table class="table">
                    <thead>
                    <th>品名</th>
                    <th>數量</th>
                    <th>單價</th>
                    </thead>
                    <tbody>
                    <tr v-for="item in order.products" :key="item.id">
                        <td class="align-middle">{{ item.product.title }}</td>
                        <td class="align-middle">{{ item.qty }}/{{ item.product.unit }}</td>
                        <td class="align-middle text-right">{{ item.final_total }}</td>
                    </tr>
                    </tbody>
                    <tfoot>
                    <tr>
                        <td colspan="2" class="text-right">總計</td>
                        <td class="text-right">{{ order.total }}</td>
                    </tr>
                    </tfoot>
                </table>

                <table class="table">
                    <tbody>
                    <tr>
                        <th width="100">Email</th>
                        <td>{{ order.user.email }}</td>
                    </tr>
                    <tr>
                        <th>姓名</th>
                        <td>{{ order.user.name }}</td>
                    </tr>
                    <tr>
                        <th>收件人電話</th>
                        <td>{{ order.user.tel }}</td>
                    </tr>
                    <tr>
                        <th>收件人地址</th>
                        <td>{{ order.user.address }}</td>
                    </tr>
                    <tr>
                        <th>付款狀態</th>
                        <td>
                        <span v-if="!order.is_paid">尚未付款</span>
                        <span v-else class="text-success">付款完成</span>
                        </td>
                    </tr>
                    </tbody>
                </table>
                <div class="text-right" v-if="order.is_paid === false">
                    <button class="btn btn-danger">確認付款去</button>
                </div>
            </form>
        </div>
    </div>
    
</div>
    
</template>

<script>
import mystoreNavbars from "../mystoreNavbar.vue";
export default{
    data(){
        return{
            order: {
                user: {},
            },
            orderId: '',

        };
    },
    components:{
        mystoreNavbars,
    },
    methods:{
        getOrder(){
            const vm = this;
            const url = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/order/${vm.orderId}`;
            vm.isLoading = true;
            /* 從api取得資料，然後... */ 
            this.$http.get(url).then((response) => {
                vm.order = response.data.order;
                console.log(response);
                vm.isLoading = false;
            });
        },
        payOrder() {
            const vm = this;
            const url = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/pay/${vm.orderId}`;
            vm.isLoading = true;
            this.$http.post(url).then((response) => {
                console.log(response);
                if (response.data.success) {
                vm.getOrder();
                }
                vm.isLoading = false;
            });
        },
    },
    created(){
        // 取得網址上的參數
       this.orderId = this.$route.params.orderId;
       this.getOrder();
       console.log(this.orderId);
    }
}
</script>

<style scoped>
.allSpace{
    padding: 50px 150px 50px 150px;
}

@media (max-width:991px){
    .allSpace{
        padding: 50px 10px 50px 10px;
    }
}
</style>