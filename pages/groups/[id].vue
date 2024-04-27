<template>
    <div v-if="pending">
        <div>Loading...</div>
    </div>
    <div v-else>
        <Navbar v-bind:is-authenticated="true" v-bind:isHome="false" v-bind:name="res.name" />

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

            <!-- <MessageForm :groupId="groupId" :username="info.res.name"/> -->
            <UForm :schema="schema" :state="state" class="space-y-4" @submit="onSubmit">
                <UFormGroup name="body">
                    <UInput v-model="state.body" placeholder="Write a Message..." />
                </UFormGroup>

                <UButton type="submit">
                    Submit
                </UButton>
            </UForm>
            
        </div>
    </div>
</template>

<script setup lang="ts">
    import { HubConnectionBuilder } from '@microsoft/signalr';
    import { object, string, type InferType } from 'yup'
    import type { FormSubmitEvent } from '#ui/types'

    useHead({
        titleTemplate: (titleChunk) => {
            return titleChunk ? `${titleChunk} - Site Title` : 'Group';
        }
    })

    const route = useRoute()
    const groupId = route.params.id

    const pending = ref(true)

    const {data:res} = await useFetch('https://cbheavin-textapp.azurewebsites.net/api/users/authenticate', {
        server: false,
        mode: 'cors',
        headers: {
            'content-type': 'application/json',
            'Access-Control-Allow-Credentials': 'true',
            'Access-Control-Allow-Origin': '*'
        },
        credentials: 'include',
    })

    const messages = ref()
    await fetch(`https://cbheavin-textapp.azurewebsites.net/api/messages/${groupId}`, {
        mode: 'cors',
        headers: {
            'content-type': 'application/json',
            'Access-Control-Allow-Credentials': 'true',
            'Access-Control-Allow-Origin': '*'
        },
        credentials: 'include',
    }).then((response) => response.json()).then((response) => messages.value = response).then(() => pending.value = false)

    //signalr
    const establishConnection = async(groupId: string | string[], msgs: []) => {
        try {
            const conn = new HubConnectionBuilder()
                .withUrl("https://cbheavin-textapp.azurewebsites.net/text")
                .build()

            conn.on("JoinGroupRoom", (groupId) => {
                console.log(groupId)
            })

            conn.on("ReceiveMessage", (msg) => {
                msgs.push(msg)
                //messages.value = [... msgs, msg]
                messages.value = msgs
            })

            await conn.start()
            await conn.invoke("JoinGroupRoom", groupId)
        }
        catch {console.log('error')}
    }

    await establishConnection(groupId, messages.value)




        
    //message form script
    const schema = object({
        body: string().required('Required'),
    })

    type Schema = InferType<typeof schema>

    const state = reactive({
        body: undefined,
    })

    async function onSubmit (event: FormSubmitEvent<Schema>) {
        const messageModel = {
            body: event.data.body,
            senderUsername: res.value.name,
            senderId: res.value.userId,
            groupId: groupId
        }

        try {
            await $fetch(`https://cbheavin-textapp.azurewebsites.net/api/messages`, {
                method: 'POST',
                server: false,
                mode: 'cors',
                headers: {
                    'content-type': 'application/json',
                    'Access-Control-Allow-Credentials': 'true',
                    'Access-Control-Allow-Origin': '*'
                },
                credentials: 'include',
                body: JSON.stringify(messageModel)
            }) 
        }
        catch{
            navigateTo('/login')
        }
    }
</script>

<style>
    @import url("~/assets/css/group.css");
</style>