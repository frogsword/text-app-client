<template>
    <div v-if="pending">
        <div>loading...</div>
    </div>
    <div v-else>
        <div v-for="message in messages">
            <div>{{ message.body }}</div>
        </div>
    </div>
</template>

<script setup lang="ts">
    const route = await useRoute()
    const groupId = await route.params.id

    const { pending, data: messages } = await useFetch(`https://localhost:7033/api/messages/${groupId}`, {
        server: false,
        //lazy: true,
        mode: 'cors',
        headers: {
            'content-type': 'application/json',
            'Access-Control-Allow-Credentials': 'true',
            'Access-Control-Allow-Origin': '*'
        },
        credentials: 'include',
    }) 
</script>