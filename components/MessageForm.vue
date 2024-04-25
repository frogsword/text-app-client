<template>
  <UForm :schema="schema" :state="state" class="space-y-4" @submit="onSubmit">
    <UFormGroup name="body">
      <UInput v-model="state.body" placeholder="Write a Message..." />
    </UFormGroup>

    <UButton type="submit">
      Submit
    </UButton>
  </UForm>
</template>

<script setup lang="ts">
    import { object, string, type InferType } from 'yup'
    import type { FormSubmitEvent } from '#ui/types'

    const props = defineProps(['groupId', 'username'])

    const schema = object({
        body: string().required('Required'),
    })

    type Schema = InferType<typeof schema>

    const state = reactive({
        body: undefined,
    })

    async function onSubmit (event: FormSubmitEvent<Schema>) {
        try {
            await $fetch(`https://localhost:7033/api/messages`, {
                method: 'POST',
                server: false,
                mode: 'cors',
                headers: {
                    'content-type': 'application/json',
                    'Access-Control-Allow-Credentials': 'true',
                    'Access-Control-Allow-Origin': '*'
                },
                credentials: 'include',
                body: JSON.stringify({
                    body: event.data.body,
                    senderUsername: props.username,
                    groupId: props.groupId
                })
            }) 
        }
        catch{
            navigateTo('/login')
        }
    }
</script>