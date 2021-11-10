<template>
  <div class="container1 container_home1">
    <h2>Mis Depositos</h2>
    <form v-on:submit.prevent="postDeposit" class="forms">
        <input type="text" v-model="deposit_data.depositer_name" placeholder="Título de transaccion">
        <br />
        <input type="text" v-model="deposit_data.note" placeholder="Nota">
        <br />
        <input type="text" v-model="deposit_data.amount" placeholder="Monto">
        <br />
        <button type="submit">Depositar</button>
        <br />
    </form>
    <div class="cont-table">
        <table class="depositos">
        <tr>
          <th>
            Titulo deposito
          </th>
          <th>
            Fecha
          </th>
          <th >
            Monto
          </th>
        </tr>
        <tr v-for="deposit in deposits">
          <td>
            {{ deposit.depositer_name }}
          </td>
          <td>
            {{ deposit.register_date.substring(0,10) }}
          </td>
          <td class="red">
              {{ deposit.amount }}
          </td>
        </tr>
      </table>
    </div>
  </div>
</template>

tener en cuenta que los datos enviado en el deposito son enteros, por lo cual se deben convertir primero,
antes de ser enviado en el body, adicionalmente el user_id y el id del account tambien deben ser enteros, 
esto se puede mejorar con un mejor planteamiento de la base de datos donde de convierten las entradas a
enteros y así acceder mas facilmente a la base de datos.
<script>
import axios from "axios";
import jwt_decode from "jwt-decode";

export default {
  name: "Deposit",

  data: function() {
    return {
        user_id: "",
        deposit_data: {
            account: "",
            amount: 0,
            // register_date: (new Date()).toJSON().toString(),
            note: "",
            depositer_name: ""
        },
        deposits: [],
    };
  },
  methods: {
    postDeposit: async function() {
      if (localStorage.getItem("token_access") === null || 
            localStorage.getItem("token_refresh") === null || this.deposit_data.data === null ){
        this.$emit('logOut');
        return;
      }
      await this.verifyToken();
      let token = localStorage.getItem("token_access");
      this.deposit_data.account = localStorage.getItem("idAccount");
      this.user_id = parseInt(jwt_decode(token).user_id.toString());
      this.deposit_data.amount = parseInt(this.deposit_data.amount)
      let url = `https://backend-bank-mintic.herokuapp.com/deposit/create/`;
      let header = { headers: { Authorization: `Bearer ${token}`}};
      let data = {
          user_id: this.user_id, 
          deposit_data: this.deposit_data
      };

      axios.post(url, data, header)
        .then((response) => {
          alert("transaccion exitosa");
          this.getDeposit();
        })
        .catch((error) => {
          console.log(error);
          alert("ocurrió algo inesperado, intente de nuevo más tarde");
          this.$emit('logOut');
        });
    },
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
    getDeposit: async function(){
        if (localStorage.getItem("token_access") === null || 
            localStorage.getItem("token_refresh") === null || this.deposit_data.data === null ){
                this.$emit('logOut');
        return;
      }
      await this.verifyToken();
      let token = localStorage.getItem("token_access");
      this.deposit_data.account = localStorage.getItem("idAccount");
      this.user_id = jwt_decode(token).user_id.toString();

      let url = `https://backend-bank-mintic.herokuapp.com/deposits/${this.user_id}/${this.deposit_data.account}/`;
      let header = { headers: { Authorization: `Bearer ${token}`}};

      axios.get(url, header)
        .then((response) => {
          alert("depositos obtenidos exitosamente");
          this.deposits = response.data
        })
        .catch((error) => {
          alert("ocurrió algo inesperado, intente de nuevo más tarde");
          this.$emit('logOut');
        });

    }
  },
  created: async function() {
    this.getDeposit();
  },
}
</script>

<style>
.container_home1 {
  position: absolute;
  top: 50%;
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

.container1 {
  margin-top: 10%;
}
.depositos {
  width: 500px;
  align-items: center;
  margin-top: 30px;
}
th, td{
  text-align: center;
}
h2{
  text-align: center;
}
.red {
  color: rgb(143, 19, 19);
}

.forms {
  position: relative;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.forms input{
  height: 40px;
  width: 100%;
  box-sizing: border-box;
  padding: 10px 20px;
  margin: 5px 0;
  border: 1px solid #283747;
}
.forms button {
  width: 100%;
  height: 40px;
  color: #e5e7e9;
  background: #283747;
  border: 1px solid #e5e7e9;
  border-radius: 5px;
  padding: 10px 25px;
  margin: 5px 0;    
}
.forms button:hover {
  color: #e5e7e9;
  background: crimson;
  border: 1px solid #283747;
}
.cont-table{
    border: 2px solid #283747;
    border-radius: 5px;
}
</style>
