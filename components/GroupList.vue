<template>
    <div class="group-list">

        <div class="group-header" v-if="isAuthenticated">
            <h1 class="group-title">Groups</h1>

            <ULink 
                to="/groups/create" 
                active-class="text-primary" 
                inactive-class="text-gray-500 dark:text-gray-400 hover:text-gray-700 dark:hover:text-gray-200" 
            >
                <UIcon name="i-heroicons-pencil-square-16-solid" class="icon" />
            </ULink>
        </div>

        <div v-for="group in groups">
            <div 
                v-on:click="navigate(group.id)" 
                class="group">
                {{ group.name }}
            </div>
        </div>

    </div>
</template>

<script lang="ts">
    export default {
        props: ['groups', 'isAuthenticated'],
        methods: {
            navigate : async function(groupId: string) {
                navigateTo(`/groups/${groupId}`)
            }
        }
    }
</script>

<style>
    @import url("~/assets/css/groupList.css");
</style>