<template>
  <div>
    <UButton label="Open" @click="isOpen = true" variant="ghost" color="sky" style="font-size: 24px;">
        <UIcon name="i-heroicons-user-group-16-solid" />
    </UButton>

    <UModal v-model="isOpen">
      <UCard :ui="{ ring: '', divide: 'divide-y divide-gray-100 dark:divide-gray-800' }">

        <template #header>
          <h1>Group Members</h1>
        </template>

        <div v-for="profile in profiles">
            <div>{{ profile.username }}</div>
        </div>

      </UCard>
    </UModal>
  </div>
</template>

<script setup lang="ts">
    const isOpen = ref(false)
    const props = defineProps({ profiles: [] })
</script>