<template>
    <div>
        <div v-if="pending">
            <NavbarSkeleton />
        </div>
        <div v-else>

            <Navbar v-bind:name="res.name" v-bind:is-authenticated="res.isAuthenticated" v-bind:isHome="true" />

            <GroupList :groups="res.groups" :isAuthenticated="res.isAuthenticated"  />
        </div>
    </div>
</template>
  
<script setup lang="js">
    useHead({
        titleTemplate: (titleChunk) => {
            return titleChunk ? `${titleChunk} - Site Title` : 'Home';
        }
    })

    //checks for user authentication. if authenticated, returns username, groups, and profile picture
    //if not authenticated, false is returned and a login/register button is shown
    const { pending, data: res } = await useFetch('https://cbheavin-textapp.azurewebsites.net/api/users/authenticate', {
        server: false,
        lazy: true,
        mode: 'cors',
        headers: {
            'content-type': 'application/json',
        },
        credentials: 'include',
    })
</script>

<style>
    @import url("~/assets/css/index.css");
</style>