<template>
    <h3>Add new transactions</h3>
    <form id="form" @submit.prevent="onSubmit">
        <div class="form-control">
            <label for="text">Text</label>
            <input type="text" id="text" v-model="text" placeholder="Enter text..." />
        </div>
        <div class="form-control">
            <label for="amount">Amount <br /> (negative - expenses, positive - income)</label>
            <input type="text" v-model="amount" id="amount" placeholder="Enter amount..." />
        </div>
        <button class="btn">Add new transaction</button>
    </form>
</template>

<script setup>
import { ref } from 'vue'
import { useToast } from 'vue-toastification'
const text = ref('');
const amount = ref('');
const toast = useToast();

const emit = defineEmits(['transactionSubmitted'])

const onSubmit = () => {
    if (!text.value || !amount.value) {
        toast.error('Please fill both fields!!');
        return;
    }
    const transactionData = {
        text: text.value,
        amount: parseFloat(amount.value)
    }

    emit('transactionSubmitted', transactionData)
    text.value = '';
    amount.value = '';
}
</script>