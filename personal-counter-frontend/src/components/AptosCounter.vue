<script async setup lang="ts">
//sender: 0x0d8b6d8c4df87920b444d398be06deb8226aba9dc3f0176e32ef6217a5094686
import { AptosClient } from "aptos"; 
import { ref, onBeforeMount } from "vue";
const moveModule = "0xf62391f707fbd748accc3d981ad37133242609c24f2f61e8e220c91bf22c7c19";
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
    <div>
        <button @click="connect()">Connect Wallet</button>
        <button @click="disconnect()">Disconnect Wallet</button>
        <p>Your Count: {{ count }}</p>
        <button @click="click()">Click</button>
        <p>Global Count: {{ gcount }}</p>
    </div>
</template>