<template>
    <div class="login">
        <div class="login-header">
            <h1 class="login-title">Login</h1>
            <ULink 
                to="/register" 
                active-class="text-primary" 
                inactive-class="text-gray-500 dark:text-gray-400 hover:text-gray-700 dark:hover:text-gray-200"
            >
                <UButton variant="outline">Register Instead</UButton>
            </ULink>
        </div>

        <div class="form">
            <UForm :schema="schema" :state="state" class="space-y-4 formgroup" @submit="onSubmit" >
                <UFormGroup name="email">
                    <UInput v-model="state.email" placeholder="Email" />
                </UFormGroup>
        
                <UFormGroup name="password">
                    <UInput v-model="state.password" type="password" placeholder="Password" />
                </UFormGroup>
        
                <UButton type="submit" class="button">
                    Login
                </UButton>
            </UForm>
        </div>
    </div>
</template>

<script setup lang="ts">
    useHead({
        titleTemplate: (titleChunk) => {
            return titleChunk ? `${titleChunk} - Site Title` : 'Login';
        }
    })

    import { object, string, type InferType } from 'yup'
    import type { FormSubmitEvent } from '#ui/types'

    const schema = object({
    email: string()
        .email('Invalid email')
        .required('Required'),
    password: string()
        .min(8, 'Must be at least 8 characters')
        .required('Required')
    })

    type Schema = InferType<typeof schema>

    const state = reactive({
        email: undefined,
        password: undefined
    })

    async function onSubmit (event: FormSubmitEvent<Schema>) {
        // Do something with event.data
        try {
            await useFetch('https://cbheavin-textapp.azurewebsites.net/api/users/login', {
                method: 'POST',
                mode: 'cors',
                headers: {
                    'content-type': 'application/json',
                },
                credentials: 'include',
                body: JSON.stringify(event.data)
            })
        }
        catch{}

        await navigateTo("/")
    }
</script>

<style>
    @import url("~/assets/css/login.css");
</style>