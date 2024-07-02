<script setup>
    import { ref } from 'vue';
    import { useAuthStore } from '@/stores/auth';
    
    const authStore = useAuthStore();
    const form = ref({email:'',password:''});
    
</script>
<template>
    <div class="row mt-5">
        <div class="col-md-4 offset-md-4">
            <form @submit.prevent="$event=>authStore.login(form)">
                <div class="card">
                    <div class="card-header bg-success border border-success text-white">
                        LOGIN
                    </div>
                    <div class="card-body">
                        <div class="input-group mb-3">
                            <span class="input-group-text">
                                <i class="fa-solid fa-at"></i>
                            </span>
                            <input v-model="form.email" type="text" placeholder="Email" class="form-control" autofocus>
                        </div>
                        <div class="input-group mb-3">
                            <span class="input-group-text">
                                <i class="fa-solid fa-key"></i>
                            </span>
                            <input v-model="form.password" type="password" placeholder="Password" class="form-control">
                        </div>
                        <div class="d-grid col-10 mx-auto">
                            <button class="btn btn-dark btn-block">Login</button>
                        </div>
                        <router-link :to="{path:'register'}">Register</router-link>
                    </div>
                </div>
            </form>
        </div>
    </div>
</template>