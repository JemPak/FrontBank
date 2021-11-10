<template>
  <div>
    <div class="header">
      <h2>Banco Misión TIC</h2>
      <nav class="container-buttons">
        <button v-if="!is_auth" v-on:click="loadLogIn"        >Iniciar Sesión </button>
        <button v-if="!is_auth" v-on:click="loadSignUp"       >Registrarse    </button>
        <button v-if="is_auth" v-on:click="loadHome"          >Cuenta         </button>
        <button v-if="is_auth" v-on:click="loadDeposit"       >Deposito       </button>
        <button v-if="is_auth" v-on:click="loadTransactions"  >Transacciones  </button>
        <button v-if="is_auth" v-on:click="logOut"            >Cerrar Sesión  </button>
      </nav>
      <button class="hamburguer">=</button>
    </div>
    <div class="main-component">
      <router-view
        v-on:completedLogIn="completedLogIn"
        v-on:completedSignUp="completedSignUp"
        v-on:logOut="logOut"
       ></router-view>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data: function () {
    return {
      is_auth: false,
    }
  },
  methods: {
    verifyAuth: function (){
      this.is_auth = localStorage.getItem("is_auth") || false;
      if(this.is_auth){
        this.loadHome();
      }else{
        this.loadLogIn();
      }
    },
    loadLogIn: function () {
      this.$router.push({ name: "logIn" });
    },
    loadSignUp: function () {
      this.$router.push({ name: "signUp" });
    },
    loadHome: function () {
      this.$router.push({ name: "home" });
    },
    loadDeposit: function (){
      this.$router.push({ name: "deposito" });
    },
    loadTransactions: function (){
      this.$router.push({ name: "transactions" });
    },
    completedLogIn: function(data) {
      console.log(data);
      localStorage.setItem("is_auth", true);
      localStorage.setItem("username", data.username);
      localStorage.setItem("token_access", data.token_access);
      localStorage.setItem("token_refresh", data.token_refresh);
      alert("Autenticación Exitosa");
      this.verifyAuth();
    },
    completedSignUp: function(data) {
      alert("Registro Exitoso");
      this.completedLogIn(data);
    },
    logOut: function(){
      localStorage.clear()
      console.log("desloguear");
      this.verifyAuth();
    }
  },
  created: function () {
    this.verifyAuth();
  },
};
</script>

<style>
body {
  margin: 0;
}
.hamburguer{
  display: none;
  background-color: #ca697a;
  font-weight: bold;
  color: white;
  border-radius: 100%;
  border: none;
  width: 30px;
  height: 30px;
  font-size: 20px;
  margin-right: 25px;
}
.header {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 10vh;
  background-color: #283747;
  color: #e5e7e9;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.header h2 {
  text-align: center;
  padding-left: 50px;
  font-family: sans-serif;
}

.container-buttons {
  margin-right: 50px;
  /* border: solid red; */
}

.container-buttons button {
  background-color: transparent;
  border: none;
  color: white;
  font-weight: bold;
  cursor: pointer;
}

*{
  font-family: sans-serif;
}

@media(max-width: 600px){
  h2{
    padding: 0 20px;
    font-size: 22px;
  }
  .container-buttons{
    display: none;
  }
  .hamburguer{
    display: block;
  }
}
    
</style>
