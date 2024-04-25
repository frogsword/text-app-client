<template>
    <div>Register</div>
    <ULink 
        to="/login" 
        active-class="text-primary" 
        inactive-class="text-gray-500 dark:text-gray-400 hover:text-gray-700 dark:hover:text-gray-200"
    >
        <UButton>Login Instead</UButton>
    </ULink>

    <UForm :schema="schema" :state="state" class="space-y-4" @submit="onSubmit">
        <UFormGroup label="Username" name="username">
            <UInput v-model="state.username" />
        </UFormGroup>

        <UFormGroup label="Email" name="email">
            <UInput v-model="state.email" />
        </UFormGroup>
  
        <UFormGroup label="Password" name="password">
            <UInput v-model="state.password" type="password" />
        </UFormGroup>

        <UFormGroup label="Confirm Password" name="confirmPassword">
            <UInput v-model="state.confirmPassword" type="password" />
        </UFormGroup>
  
        <UButton type="submit">
            Register
        </UButton>
    </UForm>
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
            await useFetch('https://localhost:7033/api/users/register', {
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

        await navigateTo("/login")
    }
</script>