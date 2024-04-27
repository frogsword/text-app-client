<template>
    <div class="create-group-main">
        <div  class="create-group-title">
            <h1>Create Group</h1>

            <ULink 
                to="/" 
                active-class="text-primary" 
                inactive-class="text-gray-500 dark:text-gray-400 hover:text-gray-700 dark:hover:text-gray-200"
            >
                <UButton>Home</UButton>
            </ULink>
        </div>

        <UForm :schema="schema" :state="state" class="space-y-4 form" @submit="onSubmit">
            <div class="form-group">
                <UFormGroup name="name">
                    <UInput v-model="state.name" placeholder="Group Name" />
                </UFormGroup>

                <UFormGroup name="user">
                    <UInput v-model="state.user" placeholder="Enter an ID of a User to Add"/>
                </UFormGroup>
            </div>
    
            <UButton type="submit" size="lg">
                Create Group
            </UButton>
        </UForm>
    </div>
</template>

<script setup lang="ts">
    useHead({
        titleTemplate: (titleChunk) => {
            return titleChunk ? `${titleChunk} - Site Title` : 'Create Group';
        }
    })

    import { array, object, string, type InferType } from 'yup'
    import type { FormSubmitEvent } from '#ui/types'

    const schema = object({
        name: string()
            .required('Required'),
        user: string()
            .required('Required')
    })

    type Schema = InferType<typeof schema>

    const state = reactive({
        name: undefined,
        user: undefined,
    })

    async function onSubmit (event: FormSubmitEvent<Schema>) {
        // Do something with event.data
        let name = event.data.name.toString()
        let members = [event.data.user.toString()]
        const body = {
            "members": members,
            "name": name
        }

        try {
            const res =  await fetch('https://cbheavin-textapp.azurewebsites.net/api/groups', {
                method: 'POST',
                mode: 'cors',
                credentials: 'include',
                headers: {
                    'content-type': 'application/json',
                },
                body: JSON.stringify(body)
            })

            navigateTo(`/`)
        }
        catch {
            alert("Unable to create group.")
        }
    }
</script>

<style>
    @import url("~/assets/css/createGroup.css");
</style>