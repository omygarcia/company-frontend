<script setup>
    import axios from 'axios';
    import { ref, onMounted } from 'vue';
    import {confirmation} from '../../functions.js';
    import { useAuthStore } from '@/stores/auth';

    const authStore = useAuthStore();
    const departments = ref([]);
    const load = ref(false);
    axios.defaults.headers.common['Authorization'] = 'Bearer '+authStore.authToken;

    onMounted(async()=>{
        await getDepartments();
    });

    const getDepartments = async()=>{
        await axios.get('/api/departments').then(response=>{
            departments.value = response.data;
            load.value = true;
        });
    }
    
    const deleteDepartment = (id,name)=>{
        confirmation(name,('/api/departments/'+id),'/departments');
    }
</script>
<template>
    <div class="row">
        <div class="col-md-4 offset-md-4">
            <div class="d-grid col-10 mx-auto">
                <router-link :to="{path:'create'}" class="btn btn-dark btn-block">
                    <i class="fa-solid fa-circle-plus"></i> Add
                </router-link>
            </div>
        </div>
    </div>
    <div class="row mt-3">
        <div class="col-md-6 offset-md-3">
            <div v-if="!load" class="card border border-white text-center">
                <div class="card-body">
                    <img src="/loading.gif" alt="loading" class="img-fluid" />
                </div>
            </div>
            <div v-else class="table-responsive">
                <table class="table table-bordered table-hover">
                    <thead class="bg-success text-white">
                        <tr>
                            <th>#</th>
                            <th>Department</th>
                            <th></th>
                            <th></th>
                        </tr>
                    </thead>
                    <tbody class="table-group-divider">
                        <tr v-for="dep,i in departments" :key="dep.id">
                            <td>{{i+1}}</td>
                            <td>{{dep.name}}</td>
                            <td>
                                <router-link :to="{name:'departments.edit',params:{id:dep.id}}" class="btn btn-warning btn-sm">
                                    <i class="fa-solid fa-edit"></i>
                                </router-link>
                            </td>
                            <td>
                                <button @click="$event=>deleteDepartment(dep.id,dep.name)" class="btn btn-danger btn-sm">
                                    <i class="fa-solid fa-trash"></i>
                                </button>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div> 
    </div>
</template>