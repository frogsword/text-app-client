<template>
    <div v-if="pending">
        <div>Loading...</div>
    </div>
    <div v-else>
        <Navbar v-bind:is-authenticated="true" v-bind:isHome="false" />

        <div class="group-page-main">
            <h1 class="group-title">Group Messages</h1>

            <div v-for="message in messages">
                <div class="message">{{ message.body }}</div>
            </div>

            <MessageForm :groupId="groupId"/>
        </div>

    </div>
</template>

<script setup lang="ts">
    useHead({
        titleTemplate: (titleChunk) => {
            return titleChunk ? `${titleChunk} - Site Title` : 'Group';
        }
    })

    const route = useRoute()
    const groupId = route.params.id

    const { pending, data: messages } = await useFetch(`https://localhost:7033/api/messages/${groupId}`, {
        server: false,
        mode: 'cors',
        headers: {
            'content-type': 'application/json',
            'Access-Control-Allow-Credentials': 'true',
            'Access-Control-Allow-Origin': '*'
        },
        credentials: 'include',
    }) 
</script>

<style>
    @import url("~/assets/css/group.css");
</style>