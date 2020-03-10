<template>
  <div id="app">
    <Nav />
    <div class="container">

      <!-- Display a loading message if loading -->
      <h1 v-if="loading" class="display-4">Loading...</h1>

      <!-- Display an error if we got one -->
      <div v-if="error">
        <h1 class="display-4">Oops!</h1>
        <p class="lead">{{error}}</p>
        <button class="btn btn-primary" @click="resetToken">Try Again &gt;</button>
      </div>

      <!-- Otherwise show our app contents -->
      <div v-else>

        <!-- If we dont have a token ask the user to authorize with YNAB -->
        <form v-if="!ynab.token">
          <h1 class="display-4">Congrats!</h1>
          <p class="lead">You have successfully started your application!</p>
          <ul>
            <li>Please go to your <a href="https://app.youneedabudget.com/settings/developer" target="_blank" rel="noopener noreferrer">YNAB Developer Settings</a> and create a new OAuth Application.</li>
            <li>Copy your Personal Access Token into <em>src/config.json</em>.</li>
          </ul>
        </form>

        <!-- If a budget has been selected, display transactions from that budget -->
        <div v-else>
          <Budget
            :category="category"
            :transactions="transactions"
          />
        </div>

      </div>
    </div>
  </div>
</template>

<script>
// Hooray! Here comes YNAB!
import * as ynab from 'ynab';

// Import our config for YNAB
import config from './config.json';

// Import Our Components to Compose Our App
import Nav from './components/Nav.vue';
import Footer from './components/Footer.vue';
import Budget from './components/Budget.vue';

export default {
  // The data to feed our templates
  data () {
    return {
      ynab: {
        clientId: config.clientId,
        redirectUri: config.redirectUri,
        token: config.personalAccessToken,
        api: null,
      },
      loading: false,
      error: null,
      budgetId: config.budgetId,
      categoryId: config.categoryId,
      category: null,
      budgets: [],
      transactions: [],
    }
  },
  // When this component is created, check whether we need to get a token,
  // budgets or display the transactions
  created() {
    if (this.ynab.token) {
      this.api = new ynab.api(this.ynab.token);
      this.loadBudget(this.budgetId);
    }
  },
  methods: {
    async loadBudget() {
      this.loading = true;
      this.error = null;
      this.transactions = [];
      const budgetId = this.budgetId;
      const categoryId = this.categoryId;
      try {
        this.category = (await this.api.categories.getCategoryById(budgetId, categoryId)).data.category;
        this.transactions = (await this.api.transactions.getTransactionsByCategory(budgetId, categoryId)).data.transactions;
        console.log(this.transactions)
      } catch (err) {
        this.error = err.error.detail;
      } finally {
        this.loading = false;
      }
    },
  },
  components: {
    Budget,
    Footer,
    Nav,
  }
}
</script>
