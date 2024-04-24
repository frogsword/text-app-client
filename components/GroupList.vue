<template>
    <div v-for="group in groups">
        <div v-on:click="navigate(group.id)">{{ group.name }}</div>
    </div>
</template>

<script lang="ts">
    export default {
        props: ['groups'],
        methods: {
            navigate : async function(groupId) {
                navigateTo(`/groups/${groupId}`)
            }
        }
    }
</script>