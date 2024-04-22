<template>
    <div>
        <div v-if="pending">
            <NavbarSkeleton />
        </div>
        <div v-else>
            <Navbar v-bind:is-authenticated=isAuthenticated />
        </div>
    </div>
</template>
  
<script setup lang="ts">
    const { pending, data: isAuthenticated} = await useLazyFetch('https://localhost:7033/api/users/authenticate', {
        server: false,
        mode: 'cors',
        headers: {
            'content-type': 'application/json',
            'Access-Control-Allow-Credentials': 'true',
            'Access-Control-Allow-Origin': '*'
        },
        credentials: 'include',
    })
</script>