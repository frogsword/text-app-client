<template>
  <div>
    <UButton @click="isOpen = true" color="yellow" variant="ghost">
        <UIcon name="i-heroicons-pencil-square-16-solid" />
    </UButton>

    <UModal v-model="isOpen">
      <div class="p-4">
        <UForm :schema="schema" :state="state" class="space-y-4" @submit="onSubmit">
            <UFormGroup name="body">
                <UInput v-model="state.body" placeholder="Edit Message" />
            </UFormGroup>

            <UButton type="submit" @click="isOpen = false">
                Submit
            </UButton>
        </UForm>
      </div>
    </UModal>
  </div>
</template>

<script setup lang="ts">
    import { object, string, type InferType } from 'yup'
    import type { FormSubmitEvent } from '#ui/types'

    const isOpen = ref(false)

    const props = defineProps(['message'])

    const schema = object({
        body: string().required('Required'),
    })

    type Schema = InferType<typeof schema>

    const state = reactive({
        body: props.message.body,
    })

    async function onSubmit (event: FormSubmitEvent<Schema>) {
    // Do something with event.data
        await fetch(`https://cbheavin-textapp.azurewebsites.net/api/messages/${props.message.id}`, {
            method: 'PUT',
            mode: 'cors',
            credentials: 'include',
            headers: {
                'content-type': 'application/json',
            },
            body: JSON.stringify(event.data.body)
        })
    }
</script>

