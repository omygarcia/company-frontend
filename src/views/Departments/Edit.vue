<script setup>
    import axios from 'axios';
    import { ref, onMounted } from 'vue';
    import {sendRequest} from '../../functions.js';
    import { useAuthStore } from '@/stores/auth';
    import { useRoute } from 'vue-router';

    const route = useRoute();

    const authStore = useAuthStore();
    axios.defaults.headers.common['Authorization'] = 'Bearer '+authStore.authToken;

    const form = ref({id:'',name:''});
    const nameInput = ref('');
    const id = ref(route.params.id);

    onMounted(()=>{
        getDepartment();
    });

    const getDepartment = ()=>{
        axios.get('api/departments/'+id.value).then((response)=>{
            form.value.name = response.data.data.name;
        });
    }

    const save = ()=>{
        sendRequest('PUT',form.value,'/api/departments/'+id.value,'/departments');
    }
</script>
<template>
    <div class="row mt-5">
        <div class="col-md-4 offset-md-4">
            <form @submit.prevent="save">
                <div class="card">
                    <div class="card-header bg-success border border-success text-white">
                    </div>
                    <div class="card-body">
                        <div class="input-group mb-3">
                            <span class="input-group-text">
                                <i class="fa-solid fa-building"></i>
                            </span>
                            <input v-model="form.name" type="text" placeholder="Department" class="form-control" autofocus
                             required>
                        </div>
                        <div class="d-grid col-10 mx-auto">
                            <button class="btn btn-dark btn-block">
                                <i class="fa-solid fa-save"></i>
                                Save
                            </button>
                        </div>
                    </div>
                </div>
            </form>
        </div>
    </div>
</template>