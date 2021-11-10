<template>
  <div class="logIn_user">
    <div class="container_logIn_user">
      <h2>Iniciar sesión</h2>
      <form v-on:submit.prevent="processLogInUser">
        <input type="text" v-model="user.username" placeholder="Username" />
        <br />
        <input type="password" v-model="user.password" placeholder="Password" />
        <br />
        <p class="error" v-if="error">Usuario o contraseña incorrectos</p>
        <button type="submit">Iniciar Sesion</button>
      </form>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "LogIn",

  data: function() {
    return {
      user: {
        username: "",
        password: "",
      },
      error: false,
    };
  },
  methods: {
    processLogInUser: async function() {
      let endpoint = "https://backend-bank-mintic.herokuapp.com/login/";
      let body = this.user;
      let params = { headers: {} };
      // Example with Async Await
      try {
        let result = await axios.post(endpoint, body, params);
        console.log(result);
        let dataLogIn = {
          username: this.user.username,
          token_access: result.data.access,
          token_refresh: result.data.refresh,
        };
        this.$emit("completedLogIn", dataLogIn);
      } catch (error) {
        this.error = true;
        if (error.response.status == 400)
          alert("Usuario o contraseña incorrectos");
        else alert("Error inesperado, intente de nuevo más tarde");
      }
    },
  },
  created: function() {},
};
</script>

<style>
.container_logIn_user {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.logIn_user {
  margin: 0;
  padding: 0%;
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
button {
  cursor: pointer;
}
.error {
  color: crimson;
  font-size: 13px;
}
</style>
