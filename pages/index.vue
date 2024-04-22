<template>
    <div>
        <div v-if="pending">
            <NavbarSkeleton />
        </div>
        <div v-else>
            <Navbar v-bind:name="res.name" v-bind:is-authenticated="res.isAuthenticated"/>
        </div>
    </div>
</template>
  
<script setup lang="js">
    const { pending, data: res } = await useLazyFetch('https://localhost:7033/api/users/authenticate', {
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