<template>
    <div class="register">
        <div class="register-header">
            <h1 class="register-title">Register</h1>
            <ULink 
                to="/login" 
                active-class="text-primary" 
                inactive-class="text-gray-500 dark:text-gray-400 hover:text-gray-700 dark:hover:text-gray-200"
            >
                <UButton variant="outline">Login Instead</UButton>
            </ULink>
        </div>

        <div class="form">
            <UForm :schema="schema" :state="state" class="space-y-4 formgroup" @submit="onSubmit">
                <UFormGroup name="username">
                    <UInput v-model="state.username" placeholder="Username" />
                </UFormGroup>

                <UFormGroup name="email">
                    <UInput v-model="state.email" placeholder="Email" />
                </UFormGroup>
        
                <UFormGroup name="password">
                    <UInput v-model="state.password" type="password" placeholder="Password" />
                </UFormGroup>

                <UFormGroup name="confirmPassword">
                    <UInput v-model="state.confirmPassword" type="password" placeholder="Confirm Password" />
                </UFormGroup>
        
                <UButton type="submit" class="button">
                    Register
                </UButton>
            </UForm>
        </div>
    </div>
</template>

<script setup lang="ts">
    useHead({
        titleTemplate: (titleChunk) => {
            return titleChunk ? `${titleChunk} - Site Title` : 'Register';
        }
    })

    import { ref, object, string, type InferType } from 'yup'
    import type { FormSubmitEvent } from '#ui/types'

    const schema = object({
        username: string()
            .required('Required'),
        email: string()
            .email('Invalid email')
            .required('Required'),
        password: string()
            .min(8, 'Must be at least 8 characters')
            .required('Required: Passwords must match.'),
        confirmPassword: string()
            .oneOf([ref('password')], 'Passwords must match')
            .min(8, 'Must be at least 8 characters')
            .required('Required: Passwords must match.')
    })

    type Schema = InferType<typeof schema>

    const state = reactive({
        username: undefined,
        email: undefined,
        password: undefined,
        confirmPassword: undefined
    })

    async function onSubmit (event: FormSubmitEvent<Schema>) {
        // Do something with event.data
        if ((event.data.confirmPassword != event.data.password) || (event.data.email == "") || (event.data.username == "")) {
            return
        }

        try {
            await useFetch('https://cbheavin-textapp.azurewebsites.net/api/users/register', {
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

        await navigateTo("/login")
    }
</script>

<style>
    @import url("~/assets/css/register.css");
</style>