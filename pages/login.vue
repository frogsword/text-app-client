<template>
    <UForm :schema="schema" :state="state" class="space-y-4" @submit="onSubmit">
        <UFormGroup label="Email" name="email">
            <UInput v-model="state.email" />
        </UFormGroup>
  
        <UFormGroup label="Password" name="password">
            <UInput v-model="state.password" type="password" />
        </UFormGroup>
  
        <UButton type="submit">
            Login
        </UButton>
    </UForm>
</template>

<script setup lang="ts">
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
            await useFetch('https://localhost:7033/api/users/login', {
                method: 'POST',
                mode: 'cors',
                headers: {
                    'content-type': 'application/json',
                    'Access-Control-Allow-Credentials': 'true',
                    'Access-Control-Allow-Origin': '*'
                },
                credentials: 'include',
                body: JSON.stringify(event.data)
            })
        }
        catch{}

        await navigateTo("/")
    }
</script>