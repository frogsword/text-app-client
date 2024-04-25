<template>
    <div class="group-list">

        <h1 class="group-title">Groups</h1>

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
        props: ['groups'],
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