<template>
    <div>
        <h1>Messenger</h1>

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

        <UButton v-show=isAuthenticated>Logout</UButton>

    </div>
</template>

<script lang="ts">
    export default {
        props: ['isAuthenticated']
    }
</script>
  