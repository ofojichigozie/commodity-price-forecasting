<template>
  <div class="main">
    <h2 class="heading text-center text-dark mt-4" style="padding: 1em 2em; color: #211c46;">
        Commodity Price Forecasting System
    </h2>
   
    <div class="d-flex justify-content-center">
        <div class="form-div">
            <h4 class="text-center" style="color: #211c46">
                Login
            </h4>
            <hr>
            <h6 class="text-center" style="color: #FF1212;">
                {{ errorMessage }}
            </h6>
            <form @submit.prevent="login">
                <label for="username">Email</label><br>
                <input type="email" name="email" v-model="email" id="email"><br>
                <label for="password">Password</label><br>
                <input type="password" name="password" v-model="password" id="password"><br>
                <input v-show="!isProcessing" type="submit" class="btn btn-dark" value="Continue"><br>
                <div v-show="isProcessing" class="text-center">
                    <img v-show="true" src="/images/loading.gif" alt="Loading">
                </div>
            </form>
        </div>
    </div>
  </div>
</template>

<script>
    import Credentials from '../data/credentials';

    export default {
        name: 'Login',
        
        data(){
            return {
                email: '',
                password: '',
                errorMessage: '',
                isProcessing: false
            }
        },

        methods: {
            login: function(){
                this.isProcessing = true;

                const email = this.email.trim();
                const password = this.password.trim();

                if((email !== '') && (password !== '')){
                    if((email == Credentials.email) && (password == Credentials.password)){
                        localStorage.setItem('loggedInAdmin', Credentials.email);
                        window.location.href = '/home';
                    }else{
                        this.errorMessage = "Unauthorized access denied";
                    }
                    
                }else{
                    this.errorMessage = "Provide login details";
                }

                this.isProcessing = false;
            }
        }
    }
</script>

<style scoped>
    .form-div{
        margin-top: 3%;
        background-color: #f2f2ff;
        color: #000000 !important;
        border: 1px solid #CCCCCC;
        padding: 1.5em;
        border-radius: 10px;
        box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
    }

    input{
        width: 100%;
        height: 40px;
        padding: 10px;
        border-radius: 5px;
        outline: 1px #a093ff solid;
        border: none;
        margin: 10px 0px;
    }

    input[type="submit"]{
        background-color: #211c46;
        color: #ffffff;
    }

    @media screen and (min-width: 760px){
        .form-div{
            width: 30% !important;
        }
    }

    @media screen and (max-width: 450px){
        .heading{
            font-size: 1.5rem;
        }
        .form-div{
            width: 90% !important;
        }
    }
</style>
