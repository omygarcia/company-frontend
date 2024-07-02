<script setup>
    import {useAuthStore} from '../stores/auth';
    const authStore = useAuthStore();
</script>
<template>
    <nav class="navbar navbar-expand-lg navbar-dark bg-success">
        <a class="navbar-brand" href="#">Company</a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarSupportedContent">
            <ul v-if="authStore.user" class="navbar-nav mr-auto">
                <li class="nav-item px-lg-5 h4">       
                    {{ authStore.user.name }}
                </li>
                <li class="nav-item px-lg-5">       
                    <RouterLink :to="{name:'departments'}" class="nav-link">Departments</RouterLink>
                </li>
                <li class="nav-item px-lg-5">
                    <RouterLink :to="{name:'employees'}" class="nav-link">Employees</RouterLink>
                </li>
                <li class="nav-item px-lg-5">
                    <RouterLink :to="{name:'employees.graphics'}" class="nav-link">Graphics</RouterLink>
                </li>
                <li class="nav-item px-lg-5">
                    <RouterLink :to="{name:'employees.reports'}" class="nav-link">Reports</RouterLink>
                </li>
            </ul>
            <ul v-if="authStore.user" class="navbar-nav me-auto mb-2 mb-lg-0">
                <li class="nav-item px-lg-5">
                    <button @click="$event => authStore.logout" class="btn btn-success">Salir</button>
                </li>
            </ul>
        </div>
    </nav>
</template>