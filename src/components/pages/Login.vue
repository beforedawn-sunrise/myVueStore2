<template>
   <div>
        <form class="form-signin" @submit.prevent="signin">
        <h1 class="h3 mb-3 font-weight-normal">登入</h1>
        <label for="inputEmail" class="sr-only">Email address</label>
        <input type="email" id="inputEmail" v-model="user.username" class="form-control" placeholder="請輸入Email.." required autofocus>
        <br>
        <label for="inputPassword" class="sr-only">Password</label>
        <input type="password" id="inputPassword" v-model="user.password" class="form-control" placeholder="請輸入密碼..." required>
        <div class="checkbox mb-3">
            <label>
            <input type="checkbox" value="remember-me"> 記得我
            </label>
        </div>
        <button class="btn btn-lg btn-primary btn-block" type="submit">登入</button>
        <p class="mt-5 mb-3 text-muted">&copy; 2017-2021</p>
        </form>
   </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      user: {
          username:'',
          password:'',
      }
    }
  },
  methods:{
      signin(){
        const api = `${process.env.APIPATH}/admin/signin`; // 'http://localhost:3000/api/casper/products';
        // API 伺服器路徑
        // 所申請的 APIPath
        const vm = this;
        console.log(process.env.APIPATH, process.env.CUSTOMPATH);

        //參考  https://www.npmjs.com/package/vue-axios
         /* 送出資料，然後... */
        this.$http.post(api,vm.user).then((response) => {
            // 由後端送token過來,然後把token轉成cookie
            const token = response.data.token;
            const expired = response.data.expired;
            
            //參考 https://developer.mozilla.org/zh-CN/docs/Web/API/Document/cookie
            console.log(response.data);
            // 如果取得資料成功,就...
            if(response.data.success){
                console.log(token,expired);
                // 把cookie寫入
                document.cookie = `hexToken=${token}; expires=${new Date(expired)};`;
                vm.$router.push('/admin/products');
            }
        });
      },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

html,
body {
  height: 100%;
}

body {
  display: -ms-flexbox;
  display: flex;
  -ms-flex-align: center;
  align-items: center;
  padding-top: 40px;
  padding-bottom: 40px;
  background-color: #f5f5f5;
}

.form-signin {
  width: 100%;
  max-width: 330px;
  padding: 15px;
  margin: auto;
}
.form-signin .checkbox {
  font-weight: 400;
}
.form-signin .form-control {
  position: relative;
  box-sizing: border-box;
  height: auto;
  padding: 10px;
  font-size: 16px;
}
.form-signin .form-control:focus {
  z-index: 2;
}
.form-signin input[type="email"] {
  margin-bottom: -1px;
  border-bottom-right-radius: 0;
  border-bottom-left-radius: 0;
}
.form-signin input[type="password"] {
  margin-bottom: 10px;
  border-top-left-radius: 0;
  border-top-right-radius: 0;
}
</style>
