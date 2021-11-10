<template>
  <div class="container container_home">
    <h2>Mi Cuenta</h2>
    <div>
      <table class="table">
        <tr>
          <th>
            Nombre
          </th>
          <th>
            Email
          </th>
          <th >
            Balance
          </th>
          <th>
            Estado
          </th>
        </tr>
        <tr>
          <td>
            {{ name }}
          </td>
          <td>
            {{ email }}
          </td>
          <td class="red">
              {{ balance }}
          </td>
          <td>
            <span v-if="isActive">Activa</span>
            <span v-if="!isActive">Inactiva</span>
          </td>
          <!-- <td class="button_delete">
            <button v-on:click="deleteAccount(account.id)">X</button>
          </td> -->
        </tr>
      </table>

      <!-- <div class="container-button">
        <button v-on:click="loadAccount" class="button_custom">
          Crear cuenta
        </button>
      </div> -->

      <!-- <router-view v-on:creationCompleted="creationCompleted"></router-view> -->
    </div>
  </div>
</template>

<script>
import axios from "axios";
import jwt_decode from "jwt-decode";

export default {
  name: "Home",

  data: function() {
    return {
      name: "",
      email: "",
      balance: 0,
      isActive: false
    }
  },
  methods: {
    // loadAccount: function() {
    //   this.$router.push({ name: "account" });
    // },
    getAccount: async function() {
      if (localStorage.getItem("token_access") === null || localStorage.getItem("token_refresh") === null) {
        this.$emit('logOut');
        return;
      }
      await this.verifyToken();
      let token = localStorage.getItem("token_access");
      let userId = jwt_decode(token).user_id.toString();

      let url = `https://backend-bank-mintic.herokuapp.com/user/${userId}/`;
      let header = { headers: { Authorization: `Bearer ${token}`}};

      axios.get(url, header)
        .then((response) => {
          this.name = response.data.name;
          this.email =response.data.email;
          this.balance =response.data.account.balance;
          this.isActive =response.data.account.isActive;
          localStorage.setItem("idAccount", parseInt(response.data.account.id));
          console.log(response);
        })
        .catch((error) => {
          console.log(error);
          alert("ocurrió algo inesperado, intente de nuevo más tarde");
          this.$emit('logOut');
        });
    },
    /*
    deleteAccount: async function(id) {
      try {
        let url = "https://banco-misionticc3p19be.herokuapp.com/account/" + id;
        let config = { headers: {} };
        await axios.delete(url, config);
        this.listAccounts();
      } catch (error) {
        console.log(error);
        alert("Ocurrió un error inesperado");
      }
    },*/
    verifyToken: function () {
        return axios.post("https://backend-bank-mintic.herokuapp.com/refresh/", {refresh: localStorage.getItem("token_refresh")}, {headers: {}}
        )
        .then((result) => {
        localStorage.setItem("token_access", result.data.access);
        })
        .catch(() => {
        this.$emit('logOut');
        });
    },
  },
  created: async function() {
    console.log("aqui toy");
    this.getAccount();
  },
}
</script>

<style>
.container_home {
  position: absolute;
  top: 40%;
  left: 50%;
  transform: translate(-50%, -50%);
  border: 3px solid #283747;
  border-radius: 10px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: auto;
  padding: 50px;
}

.container {
  margin-top: 50px;
}
.table {
  width: 500px;
  align-items: center;
  margin-top: 25px;
}
th, td{
  text-align: center;
}
h2{
  text-align: center;
}
.green {
  color: rgb(2, 15, 44);
}
.red {
  color: rgb(143, 19, 19);
}
.button_delete {
  text-align: center;
}
.button_custom {
  color: #e5e7e9;
  background: #283747;
  border: 1px solid #e5e7e9;
  border-radius: 5px;
  padding: 10px 25px;
  margin: 5px 0;
}
.container-button {
  text-align: center;
}
</style>
