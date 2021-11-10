<template>
  <div class="container2 container_home2">
    <h2>Realizar transferencia electrónica</h2>
    <form v-on:submit.prevent="processTransaction" class="forms2">
        <input type="text" v-model="transaction.transaction_data.note" placeholder="Título de transaccion">
        <br />
        <input type="text" v-model="transaction.transaction_data.email_destiny_account" placeholder="Email contacto a Transferir">
        <br />
        <input type="text" v-model="transaction.transaction_data.amount" placeholder="Monto">
        <br />
        <button type="submit">Transferir</button>
        <br />
    </form>
    <div class="table-transfer">
        <table class="transferencias">
          <tr>
            <th>
              Fecha
            </th>
            <th>
              Monto
            </th>
            <th >
              Dirección de destino
            </th>
          </tr>
          <tr v-for="transactions in myTransactions">
            <td>
              {{ transactions.register_date.substring(0,10) }}
            </td>
            <td>
              {{ transactions.amount}}
            </td>
            <td class="red">
                {{ transactions.destiny_account.email }}
            </td>
          </tr>
        </table>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import jwt_decode from "jwt-decode";
export default {
  name: "transactions",
  data: function() {
    return {
      transaction: {
        transaction_data: {
          origin_account: 0,
          email_destiny_account: "",
          amount: 0,
          note: ""
          // register_date: new Date().toJSON().toString(),
        },
      },
      myTransactions: [],
    };
  },
  methods: {
    processTransaction: async function() {
      if (localStorage.getItem("token_refresh") === null ||
           localStorage.getItem("token_access") === null ||
             localStorage.getItem("idAccount") === null) {
        this.$emit("logOut");
        return;
      }
      await this.verifyToken();
      let token = localStorage.getItem("token_access");
      this.transaction.transaction_data.origin_account = parseInt(jwt_decode(token).user_id.toString());
      this.transaction.transaction_data.amount = parseInt(this.transaction.transaction_data.amount);
      axios
        .post("https://backend-bank-mintic.herokuapp.com/transaction/create/", this.transaction, {
          headers: { Authorization: `Bearer ${token}` },
        })
        .then((result) => {
          alert("Transación Exitosa !!");
          this.getMyTransactions();
        })
        .catch((error) => {
          console.log("Error");
          if (error.response.status == "401") {
            alert("Usted no está autorizado para realizar esta operación.");
          } else if (error.response.status == "400") {
            alert(
              "La transacción no se pudo procesar correctamente.\nRevise todos los datos e intente de nuevo."
            );
          }
        });
    },
    verifyToken: async function() {
      return axios
        .post(
          "https://backend-bank-mintic.herokuapp.com/refresh/",
          { refresh: localStorage.getItem("token_refresh") },
          { headers: {} }
        )
        .then((result) => {
          localStorage.setItem("token_access", result.data.access);
        })
        .catch((error) => {
          this.$emit("logOut");
        });
    },
    getMyTransactions: async function() {
      // verificar si la idAccount esta guardada en el localStorage
      if (localStorage.getItem("token_refresh") === null ||
            localStorage.getItem("token_access") === null ||
             localStorage.getItem("idAccount") === null) {
        this.$emit("logOut");
        return;
      }
      await this.verifyToken();
      let token = localStorage.getItem("token_access");
      let userId = jwt_decode(token).user_id.toString();
      let idAccount = localStorage.getItem("idAccount")
      axios
        .get(`https://backend-bank-mintic.herokuapp.com/transactions/${userId}/${idAccount}/`, {
          headers: { Authorization: `Bearer ${token}` },
        })
        .then((result) => {
          this.myTransactions = result.data;
        })
        .catch((error) => {
          if (error.response.status == "401") {
            alert("Usted no está autorizado para realizar esta operación.");
          } else if (error.response.status == "500") {
            alert(
              "La plataforma está presentando problemas.\nIntente de nuevo más tarde."
            );
          }
        });
    },
  },
  created: async function() {
    this.getMyTransactions();
  },
};
</script>

<style>
.container_home2 {
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

.container2 {
  margin-top: 50px;
}
.transferencias {
  width: 450px;
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

.forms2 {
  position: relative;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.forms2 input{
  height: 40px;
  width: 100%;
  box-sizing: border-box;
  padding: 10px 20px;
  margin: 5px 0;
  border: 1px solid #283747;
}
.forms2 button {
  width: 100%;
  height: 40px;
  color: #e5e7e9;
  background: #283747;
  border: 1px solid #e5e7e9;
  border-radius: 5px;
  padding: 10px 25px;
  margin: 5px 0;    
}
.forms2 button:hover {
  color: #e5e7e9;
  background: crimson;
  border: 1px solid #283747;
}
.table-transfer{
    border: 2px solid #283747;
    border-radius: 5px;
}
</style>
