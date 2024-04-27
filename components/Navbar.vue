<template>
    <div class="navbar">
        <h1 class="header">
            Welcome, {{ name }}
        </h1>

        <div class="button-group">
            <ULink 
                to="/login" 
                active-class="text-primary" 
                inactive-class="text-gray-500 dark:text-gray-400 hover:text-gray-700 dark:hover:text-gray-200"
            >
                <UButton v-show=!isAuthenticated>Login</UButton>
            </ULink>

            <ULink 
                to="/register" 
                active-class="text-primary" 
                inactive-class="text-gray-500 dark:text-gray-400 hover:text-gray-700 dark:hover:text-gray-200"
            >
                <UButton v-show=!isAuthenticated>Register</UButton>
            </ULink>
            
            <UButton v-on:click="handleLogout" v-show=isAuthenticated>
                Logout
            </UButton>

            <ULink 
                to="/" 
                active-class="text-primary" 
                inactive-class="text-gray-500 dark:text-gray-400 hover:text-gray-700 dark:hover:text-gray-200"
            >
                <UButton v-show=!isHome>Home</UButton>
            </ULink>
        </div>
    </div>
</template>

<script lang="ts">
    export default {
        props: ['name', 'isAuthenticated', 'isHome'],
        methods: {
            handleLogout : async function() {
                await useFetch('https://cbheavin-textapp.azurewebsites.net/api/users/logout', {
                    mode: 'cors',
                    server : false,
                    headers: {
                        'content-type': 'application/json',
                        // 'Access-Control-Allow-Credentials': 'true',
                        // 'Access-Control-Allow-Origin': '*'
                    },
                    credentials: 'include',
                })
                await navigateTo("/login")
            }
        }
    }
</script>
  
<style>
    @import url("~/assets/css/navbar.css");
</style>