<script  async setup lang="ts">
    import { AptosClient } from 'aptos';
    import { ref, onBeforeMount } from 'vue';
    const moveModule = "0xcbd18ab8e5e9c7c3fea68162977120c9f7e7abbd3cafa56f9b1694e7ac88506f";
    const client = new AptosClient("https://fullnode.devnet.aptoslabs.com/v1");

    const count = ref(0);
    const totalcount = ref();
    const walletAddress = ref("");

    const a = async () => {
        const resources = await client.getAccountResources(walletAddress.value);
        const resourceType = `${moveModule}::counter::CountHolder`;
        const resource = resources.find((el) => el.type === resourceType);
        console.log("global",resource?.data.count);
        return resource?.data.count;
    };

    const getTotalBumps = async () => {
        const resources = await client.getAccountResources(moveModule)
        const resourceType = `${moveModule}::counter::TotalBumps`;
        const resource = resources.find((el) => el.type === resourceType);
        console.log(resource.data.total);
        return resource.data.total;
    };

    const bump = async () => {
        const payload = {
            function: `${moveModule}::counter::bump`,
            type_arguments: [],
            arguments: [],
        };
        const transaction = await window.martian.generateTransaction(
            walletAddress.value,
            payload
        );
        const txnHash = await window.martian.signAndSubmitTransaction(transaction);
    };

    onBeforeMount(async () => {
        const wallet = await window.martian.connect();
        walletAddress.value = wallet.address;
        count.value = await a();
        totalcount.value = await getTotalBumps();
    })

    a();
</script>
<template>
    <div>
        <p>Count: {{ totalcount }}</p>
        <button @click="bump()">bump</button>
        <p>Total Bumps: {{ count }}</p>
    </div>
</template>