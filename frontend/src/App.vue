<script>
const API_BASE_URL = "http://localhost/services/";
let API_NAME = "";

export default {
  data() {
    return {
      email: "",
      errorMessage: "",
      usersEmails: [],
      orders: [],
    };
  },
  mounted: function () {
    this.getusersEmails();
  },
  methods: {
    onSubmit(event) {
      event.preventDefault();
      if (!this.validateEmail(this.email)) return false;
      this.getOrders(this.email);
    },
    onFocus(event) {
      this.errorMessage = "";
      this.email = "";
    },
    validateEmail(email) {
      if (/^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/.test(email)) {
        this.errorMessage = "";
        return true;
      } else {
        this.errorMessage = "Email non corretta";
        this.orders = [];
        return false;
      }
    },
    callApi(attributeName, pars) {
      let asyncCall = new Promise(async (resolve) => {
        let qs = pars ? "?" + new URLSearchParams(pars).toString() : "";
        let apiResponse = await fetch(`${API_BASE_URL}${API_NAME}.php${qs}`);
        let data = await apiResponse.json();
        resolve(data);
      }).then((data) => {
        this[attributeName] = data.orders;
      });
    },
    getOrders(email) {
      API_NAME = "ordersApi";
      this.callApi("orders", { email: email });
    },
    getusersEmails() {
      API_NAME = "emailsApi";
      this.callApi("usersEmails");
    },
  },
};
</script>

<template>
  <div id="container">
    <div>
      <form @submit="onSubmit" class="add-form">
        <label for="email">
          <h3>Ricerca ordini per email</h3>
          <span class="error">{{ errorMessage }}</span>
          <br />
        </label>
        <input
          @focus="onFocus"
          placeholder="Inserire qui la email"
          v-model="email"
          required
          type="email"
        />
        <input type="Submit" value="Invia" class="btn-btn-block" />
      </form>
      <h3>Email utenti</h3>
      <li v-for="item in usersEmails">
        {{ item.email }}
      </li>
    </div>
    <div>
      <p v-if="!orders"><b>Nessun'ordine trovato per questa email</b></p>
      <table v-if="orders && orders.length">
        <tr>
          <th>Utente</th>
          <th>Articolo</th>
          <th>Data ordine</th>
        </tr>
        <tr v-for="(item, index) in orders">
          <td>{{ item.nome }} {{ item.cognome }}</td>
          <td>{{ item.productName }}</td>
          <td>{{ item.createdAt }}</td>
        </tr>
      </table>
    </div>
  </div>
</template>

<style>
.error {
  color: red;
}
label h3 {
  display: inline-block;
  margin-right: 10px;
}
th {
  text-align: left;
}
table,
td,
th {
  border-collapse: collapse;
  border: 1px solid grey;
}
td,
th {
  padding: 4px;
}
#container div {
  display:inline-block;
  vertical-align: top;
  margin: 0px 15px;
  padding-top:15px;
}
#container {

    background: #fff;
    padding: 20px;
    margin: 40px;
    flex: 1 0 auto;
    width: 800px;
    -webkit-box-shadow: 2px 0 5px -2px #ddd;
    -moz-box-shadow: 2px 0 5px -2px #ddd;
    box-shadow: 2px 0 5px -2px #aaa;
}
.btn-btn-block {
  font-family: '"Lyon Text OSF Web", Georgia, "Times New Roman", Times, serif';
  background-color: rgb(0, 123, 181);
  border: 1px solid rgb(0, 123, 181);
  padding: 8px 56px;
  color:white;
  cursor: pointer;
}
input:focus {    
  border-color: #66afe9;
    outline: 0;
    -webkit-box-shadow: inset 0 1px 1px rgb(0 0 0 / 8%), 0 0 8px rgb(102 175 233 / 60%);
    box-shadow: inset 0 1px 1px rgb(0 0 0 / 8%), 0 0 8px rgb(102 175 233 / 60%);
}
input { 
border-radius: 0;
    border: 1px solid #EAE8E8;
    padding: 10px 10px;
    font-size: 18px;
    line-height: 26px;
    box-shadow: none;
    -webkit-box-shadow: none;
    height: auto;
    background-color: #ddd
}
body {background-color: #eee;}
</style>
