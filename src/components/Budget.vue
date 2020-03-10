<template>
  <div class="container">
    <h5>HSA Balance: {{ computedCategory.balance / 1000 }}</h5>
    <table class="table">
      <thead>
        <tr>
          <th class="cleared">Cleared</th>
          <th>Date</th>
          <th class="memo">Memo</th>
          <th>Amount</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="transaction in transactions" v-bind:key="transaction.id">
          <td class="cleared">
            <CheckIcon v-if="transaction.cleared === 'cleared'" />
          </td>
          <td>{{ transaction.date }}</td>
          <td>{{ transaction.memo }}</td>
          <td class="amount">
            {{
              convertMilliUnitsToCurrencyAmount(transaction.amount).toFixed(2)
            }}
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
// Import utils from YNAB
import { utils } from 'ynab';

import CheckIcon from './CheckIcon.vue';
export default {
  components: {
    CheckIcon
  },
  props: {
    category: Object,
    transactions: Array
  },
  methods: {
    // Now we can make this method available to our template
    // So we can format this milliunits in the correct currency format
    convertMilliUnitsToCurrencyAmount: utils.convertMilliUnitsToCurrencyAmount
  },
  computed: {
    computedCategory() {
      return this.category || {};
    }
  }
};
</script>

<style scoped lang="scss">
.table {
  th, td {
    white-space: nowrap;
  }
  td.cleared {
    padding: none;
    color: green;
  }
  td.amount {
    text-align: right;
  }
  th.memo {
    width: 100%;
  }
}
</style>
