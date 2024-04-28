<template>
  <div>
    <UButton @click="isOpen = true" variant="ghost" color="rose" style="font-size: 24px;">
        <UIcon name="i-heroicons-pencil-square-16-solid" />
    </UButton>

    <UModal v-model="isOpen">
      <div class="p-4">
        <UForm :schema="schema" :state="state" class="space-y-4" @submit="onSubmit">

            <UFormGroup name="name">
                <UInput v-model="state.name" placeholder="Change Group Name"/>
            </UFormGroup>

            <UButton @click="isOpen = false" type="submit">
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

    const props = defineProps({
        groupId: string
    })

    const schema = object({
        name: string().required('Required'),
    })

    type Schema = InferType<typeof schema>

    const state = reactive({
        name: undefined,
    })

    async function onSubmit (event: FormSubmitEvent<Schema>) {
        try {
            await fetch(`https://cbheavin-textapp.azurewebsites.net/api/groups/${props.groupId}`,  {
                method: 'PATCH',
                mode: 'cors',
                credentials: 'include',
                headers: {
                    'content-type': 'application/json',
                },
                body: JSON.stringify(event.data.name)
            })
        }
        catch {
            alert("Unable to change group name.")
        }
    }
</script>