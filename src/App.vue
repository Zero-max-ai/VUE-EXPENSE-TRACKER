<template>
    <Header />
    <div class="container">
        <Balance :total="+  total" />
        <IncomeExpenses :income="+income" :expenses="+expenses" />
        <TransactionList @transactionDeleted="handleTranasctionDeleted" :transactions="transactions" />
        <AddTransaction @transactionSubmitted="handleTranasctionSubmitted" />
    </div>
</template>

<script setup>
import Header from './components/Header.vue'
import Balance from './components/Balance.vue'
import IncomeExpenses from './components/IncomeExpenses.vue'
import TransactionList from './components/TransactionList.vue'
import AddTransaction from './components/AddTransaction.vue'

import { useToast } from 'vue-toastification'

import { ref, computed, onMounted } from 'vue';

onMounted(() => {
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'));
    if (savedTransactions) {
        transactions.value = savedTransactions;
    }
})

const toast = useToast();

const transactions = ref([]);

const total = computed(() => {
    return transactions.value.reduce((acc, transaction) => {
        return acc + transaction.amount;
    }, 0).toFixed(2)
});

const income = computed(() => {
    return transactions.value
        .filter((transaction) => transaction.amount > 0)
        .reduce((acc, transaction) => {
            return acc + transaction.amount;
        }, 0)
        .toFixed(2);
});

const expenses = computed(() => {
    return transactions.value
        .filter((transaction) => transaction.amount < 0)
        .reduce((acc, transaction) => {
            return acc + transaction.amount;
        }, 0)
        .toFixed(2);
});

const handleTranasctionSubmitted = (transactionData) => {
    transactions.value.push({
        id: generateUniqueId(),
        text: transactionData.text,
        amount: transactionData.amount,
    });
    saveTransactionsToLocalStorage();
    toast.success('Transaction added');
}

// Generate Unique id
const generateUniqueId = () => {
    return Math.floor(Math.random() * 1000000);
}

// Delete Transaction
const handleTranasctionDeleted = (id) => {
    transactions.value = transactions.value.filter((transaction) => transaction.id != id)

    saveTransactionsToLocalStorage();
    toast.success('Transaction deleted')
}

// Save to local storage
const saveTransactionsToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value));
}

</script>