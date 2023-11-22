<script async setup lang="ts">

import { AptosClient } from "aptos"; 
import { ref, onBeforeMount } from "vue";
const moveModule = "0xf2168be2cd7fe303b6acf9c1f1039c0bcdedc8c94587db584667d7a8f344c3de";
const client = new AptosClient("https://fullnode.devnet.aptoslabs.com/v1");

const count = ref(0);
const walletAddress = ref("");
const gcount = ref();


const getCount = async () => {
    const resources = await client.getAccountResources(walletAddress.value)
    const resourceType = `${moveModule}::counter::CountHolder`;
    const resource = resources.find((el) => el.type === resourceType);
    console.log(resource.data.count);
    return resource.data.count;
};

const globalCount = async () => {
    const resources = await client.getAccountResources(moveModule);
    const resourceType = `${moveModule}::counter::CountHolder`;
    const resource = resources.find((el) => el.type === resourceType);
    console.log(resource.data.count);
    return resource.data.count;
}

const click = async () => {
    const payload = {
        function: `${moveModule}::counter::click`,
        type_arguments: [],
        arguments: [],
    };
    const transaction = await window.martian.generateTransaction(
        walletAddress.value,
        payload
    );
    const txnHash = await window.martian.signAndSubmitTransaction(transaction);
};

const connect = async () => {
    const wallet = await window.martian.connect();
    console.log("Wallet Connected");
}

const disconnect = async () => {
    await window.martian.disconnect();
    console.log("Wallet Disconnected");
}

onBeforeMount(async () => {
    const wallet = await window.martian.connect();
    walletAddress.value = wallet.address;
    count.value = await getCount();
    gcount.value = await globalCount();
    
})

</script>

<template>
    <div class="counter-container">
        <h1 class="counter-text" style="font-size: 3em;">Counter Game</h1>
        <button class="counter-button" @click="connect()">Connect Wallet</button>
        <button class="counter-button" @click="disconnect()">Disconnect Wallet</button>
        <button class="counter-button circular-button" @click="click()">Click</button>
        <p class="counter-text">Your Clicks: {{ count }}</p>
        <p class="counter-text">Global Clicks: {{ gcount }}</p>
    </div>
</template>

<style scoped>
.counter-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
}

.counter-button {
    margin: 10px;
    padding: 10px 20px;
    font-size: 1.2em;
    color: #fff;
    background-color: #f00;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

.counter-button:hover {
    background-color: #d00;
}

.counter-text {
    color: #f00;
    font-size: 2em;
    font-family: 'Digital-7 Mono', sans-serif;
}

.circular-button {
    width: 100px; 
    height: 100px; 
    border-radius: 50%; 
    font-size: 1.5em; 
    display: flex;
    align-items: center;
    justify-content: center;
}
</style>