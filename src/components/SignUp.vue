<template>
  <div class="logIn_user">
    <div class="container_logIn_user">
      <h2>Registrarse</h2>
      <form v-on:submit.prevent="signUpClient">
        <input v-model="user.username" type="text" placeholder="Username" />
        <br />
        <input  v-model="user.password" type="password" placeholder="Password" />
        <br />
        <input type="password" placeholder="Confirm Password" />
        <br />
        <input type="text" v-model="user.name" placeholder="Name">
        <br>
        <input type="email" v-model="user.email" placeholder="Email">
        <br>
        <input type="number" v-model="user.account.balance" placeholder="Initial Balance">
        <br>
        <p v-if="failed" class="errorMessage">Registro Incorrecto</p>
        <button type="submit">Registrarse</button>
      </form>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {

  name: "SignUp",

  data: function () {
    return {
        user: {
          username:  "",
          password: "",
          name: "",
          email: "",
          account: {
            lastChangeDate: (new Date()).toJSON().toString(),
            balance: 0,
            isActive: true
            }
        },        
        failed: false
    }
  },
  methods: {
      signUpClient: function(){
          console.log("REGISTRANDO EL USUARIO CON DATOS:")
          console.log(this.user)
          //change host
          let url = "https://backend-bank-mintic.herokuapp.com/user/create/";
          this.user.account.balance = parseInt(this.user.account.balance)
          let body = this.user;
          let config = {headers: {}};
          // Example without Async - Await
          axios.post(url, body, config)
            .then((result) => {
              let dataSignUp = {
                username: this.user.username,
                token_access: result.data.access,
                token_refresh: result.data.refresh,
              }
              this.failed = false;
              // alert("Usuario registrado");
              console.log(dataSignUp)
              this.$emit('completedSignUp',dataSignUp)
            }).catch((error) => {
                this.failed = true;
                console.log(error.response)
                alert("Error inesperado, intente de nuevo más tarde")
            });
      }
  },
  created: function () {},
}
</script>

<style>
.errorMessage{
    font-family: sans-serif;
    font-size: 13px;
    color: crimson;
}
.container_logIn_user {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  min-width: 350px;
}
.logIn_user {
  margin: 0;
  padding: 0;
  height: 100%;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
.container_logIn_user {
  position: absolute;
  border: 3px solid #283747;
  border-radius: 10px;
  width: 25%;
  height: 70%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.logIn_user h2 {
  padding-top: 10px;
  color: #283747;
}
.logIn_user form {
  width: 70%;
}
.logIn_user input {
  height: 40px;
  width: 100%;
  box-sizing: border-box;
  padding: 10px 20px;
  margin: 5px 0;
  border: 1px solid #283747;
}
.logIn_user button {
  width: 100%;
  height: 40px;
  color: #e5e7e9;
  background: #283747;
  border: 1px solid #e5e7e9;
  border-radius: 5px;
  padding: 10px 25px;
  margin: 5px 0;
}
.logIn_user button:hover {
  color: #e5e7e9;
  background: crimson;
  border: 1px solid #283747;
}
button{
    cursor: pointer;
}
</style>