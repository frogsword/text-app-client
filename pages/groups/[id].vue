<template>
    <div v-if="pending">
        <div>Loading...</div>
    </div>
    <div v-else>
        <Navbar v-bind:is-authenticated="true" v-bind:isHome="false" />

        <div class="group-page-main">
            <h1 class="group-title">Group Messages</h1>

            <div v-for="message in messages">
                <div v-if="message.sender == res.userId" class="message sender">
                    <div class="sender-info">
                        {{ message.senderUsername }} 
                        <span class="date">
                            {{ message.createdAt.slice(11,16) }}
                        </span>
                    </div>
                    <div>{{ message.body }}</div>
                </div>
                
                <div v-else class="message">
                    <div class="receiver-info">
                        {{ message.senderUsername }} 
                        <span class="date">
                            {{ message.createdAt.slice(11,16) }}
                        </span>
                    </div>
                    <div>{{ message.body }}</div>
                </div>
            </div>

            <MessageForm :groupId="groupId" :username="res.name"/>
        </div>

    </div>
</template>

<script setup lang="js">
    useHead({
        titleTemplate: (titleChunk) => {
            return titleChunk ? `${titleChunk} - Site Title` : 'Group';
        }
    })

    const route = useRoute()
    const groupId = route.params.id

        //use userId to determine if user is sender of message
        //if unauthenticated, go to login page
        const { pending, data: res } = await useFetch('https://localhost:7033/api/users/authenticate', {
            server: false,
            mode: 'cors',
            headers: {
                'content-type': 'application/json',
                'Access-Control-Allow-Credentials': 'true',
                'Access-Control-Allow-Origin': '*'
            },
            credentials: 'include',
        })

        const { data: messages } = await useFetch(`https://localhost:7033/api/messages/${groupId}`, {
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