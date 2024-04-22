<template>
    <div>
        <h1 v-if="isAuthenticated">Welcome, {{ name }}</h1>
        <h1 v-else>Please Login to Continue</h1>

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
        
        <UButton v-on:click="handleLogout" v-show=isAuthenticated>Logout</UButton>
    </div>
</template>

<script lang="ts">
    export default {
        props: ['name', 'isAuthenticated'],
        methods: {
            handleLogout : async function() {
                console.log('click')
                await useFetch('https://localhost:7033/api/users/logout', {
                    mode: 'cors',
                    server : false,
                    headers: {
                        'content-type': 'application/json',
                        'Access-Control-Allow-Credentials': 'true',
                        'Access-Control-Allow-Origin': '*'
                    },
                    credentials: 'include',
                })
                await navigateTo("/login")
            }
        }
    }
</script>
  