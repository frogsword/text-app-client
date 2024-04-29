<template>
    <div v-if="pending">
        <div>Loading...</div>
    </div>
    <div v-else>
        <div class="group-page-main">
            <div class="group-title">

                <h1 class="group-name">
                    {{ groupInfo.name }}
                    <ChangeGroupNameModal :groupId="groupId" />
                </h1>

                <div class="button-group">
                    <ULink 
                        to="/" 
                        active-class="text-primary" 
                        inactive-class="text-gray-500 dark:text-gray-400 hover:text-gray-700 dark:hover:text-gray-200"
                    >
                        <UButton variant="ghost" style="font-size: 24px;">
                            <UIcon name="i-heroicons-home" />
                        </UButton>
                    </ULink>

                    <GroupMembersModal :profiles="groupInfo.profiles" />

                </div>
            </div>

            <div v-for="message in messages">

                <div v-if="!message.isDeleted">
                    <div v-if="message.sender == res.userId" class="message sender">
                        <div class="sender-info">
                            <UButton @click="deleteMessage(message.id)" color="red" variant="ghost"><UIcon name="i-heroicons-trash" /></UButton>
                            {{ message.senderUsername }} 
                            <span class="date">
                                {{ message.createdAt.slice(11,16) }}
                            </span>
                        </div>
                    <div class="message-body">
                        <EditMessageModal :message="message" />
                        {{ message.body }}
                    </div>
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

            </div>

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
            return titleChunk ? `${titleChunk} - Site Title` : "Group";
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
        },
        credentials: 'include',
    })

    const groupInfo = ref()
    await fetch(`https://cbheavin-textapp.azurewebsites.net/api/groups/${groupId}`, {
        mode: 'cors',
        credentials: 'include',
        headers: {
            'content-type': 'application/json',
        },
    })
    .then((res) => res.json())
    .then((res) => groupInfo.value = res)

    const messages = ref()
    await fetch(`https://cbheavin-textapp.azurewebsites.net/api/messages/${groupId}`, {
        mode: 'cors',
        credentials: 'include',
        headers: {
            'content-type': 'application/json',
        }
    }) 
    .then((response) => response.json())
    .then((response) => messages.value = response)
    .then(() => pending.value = false)



    //signalr
    // const establishConnection = async(groupId: string | string[], msgs: []) => {
        try {
            const conn = new HubConnectionBuilder()
                .withUrl("https://cbheavin-textapp.azurewebsites.net/text")
                .build()

            conn.on("JoinGroupRoom", (groupId) => {
                console.log(groupId)
            })

            conn.on("ReceiveMessage", (msg) => {
                const msgs = messages.value
                msgs.push(msg)
                messages.value = msgs
            })

            conn.on("UpdateGroupMessages", (msgs) => {
                messages.value = msgs
            })

            await conn.start()
            await conn.invoke("JoinGroupRoom", groupId)
        }
        catch {console.log('error')}
    // }
    // await establishConnection(groupId, messages.value)


        
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
                },
                credentials: 'include',
                body: JSON.stringify(messageModel)
            }) 
        }
        catch{
            navigateTo('/login')
        }
    }

    async function deleteMessage(messageId: string) {
        try {
            await fetch(`https://cbheavin-textapp.azurewebsites.net/api/messages/${messageId}`, {
                method: 'DELETE',
                mode: 'cors',
                credentials: 'include',
                headers: {
                    'content-type': 'application/json',
                }
            })
            // .then(() => {
            //     for (let i = 0; i < messages.value.length; i++) {
            //         if (messages.value[i].id == messageId) {
            //             messages.value[i].isDeleted = true
            //         }
            //     }
            // })
        }
        catch {
            alert("Unable to delete message.")
        }
    }
</script>

<style>
    @import url("~/assets/css/group.css");
</style>