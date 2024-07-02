<script setup>
    import axios from 'axios';
    import { ref, onMounted, nextTick } from 'vue';
    import {confirmation, sendRequest} from '../../functions.js';
    import { useAuthStore } from '@/stores/auth';
    import Modal from '@/components/Modal.vue';
    import SelectInput from '@/components/SelectInput.vue';
    import Paginate from 'vuejs-paginate-next';

    const authStore = useAuthStore();
    axios.defaults.headers.common['Authorization'] = 'Bearer '+authStore.authToken;

    const departments = ref([]);
    const employees = ref([]);
    const load = ref(false);
    const rows = ref(0);
    const form = ref({
        name:'',email:'',phone:'',department_id:''
    });
    const title = ref('');
    const nameInput = ref('');
    const operation = ref(1);
    const id = ref('');
    const close = ref([]);

    onMounted(async()=>{
        await getDepartments();
        getEmployees(1);
    });

    const getDepartments = async()=>{
        await axios.get('/api/departments').then(response=>{
            departments.value = response.data;
        });
    }

    const getEmployees = async(page = 1)=>{
        await axios.get('/api/employees',{params:{page}}).then(response=>{
            employees.value = response.data.data;
            rows.value = response.data.last_page;
            load.value = true;
        });
    }
    
    const deleteEmployee = (id,name)=>{
        confirmation(name,('/api/employees/'+id),'/employees');
    }

    const openModal = (op,name,email,phone,department,employee)=>{
        clear();
        setTimeout(() => nextTick(()=>nameInput.value.focus()), 600);
        operation.value = op;
        id.value = employee;
        if(op == 1){
            title.value = 'Create employee';
        }
        else{
            title.value = 'Edit employee';
            form.value.name = name;
            form.value.email = email;
            form.value.phone = phone;
            form.value.department_id = department;
        }
    }

    const clear = ()=>{
        form.value.name = '';
        form.value.email = '';
        form.value.phone = '';
        form.value.department_id = '';
    }

    const save = async()=>{
        let res;
        if(operation.value == 1)
        {
            res = await sendRequest('POST',form.value,'/api/employees','');
            if(res==true)
            {
                clear();
                nextTick(()=>nameInput.value.focus());
                getEmployees(1);
            }
        }else{
            res = await sendRequest('PUT',form.value,('/api/employees'+id.value),'');
            if(res==true)
            {
                nextTick(()=>close.value.click());
                getEmployees(1);
            }
        }
    }
</script>
<template>
    <div class="row">
        <div class="col-md-4 offset-md-4">
            <div class="d-grid col-10 mx-auto">
                <button class="btn btn-dark btn-block" data-toggle="modal" data-target="#modal" @click="$event=>openModal(1)">
                    <i class="fa-solid fa-circle-plus"></i> Add
                </button>
            </div>
        </div>
    </div>
    <div class="row mt-3">
        <div class="col-md-10 offset-md-1">
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
                            <th>NAME</th>
                            <th>EMAIL</th>
                            <th>PHONE</th>
                            <th>DEPARTMENT</th>
                            <th></th>
                            <th></th>
                        </tr>
                    </thead>
                    <tbody class="table-group-divider">
                        <tr v-for="empl,i in employees" :key="empl.id">
                            <td>{{i+1}}</td>
                            <td>{{empl.name}}</td>
                            <td>{{empl.email}}</td>
                            <td>{{empl.phone}}</td>
                            <td>{{empl.department}}</td>
                            <td>
                                <button @click="openModal(2,empl.name,empl.email,empl.phone,empl.department_id,empl.id)" class="btn btn-warning btn-sm" data-toggle="modal" data-target="#modal">
                                    <i class="fa-solid fa-edit"></i>
                                </button>
                            </td>
                            <td>
                                <button @click="$event=>deleteEmployee(empl.id,empl.name)" class="btn btn-danger btn-sm">
                                    <i class="fa-solid fa-trash"></i>
                                </button>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <Paginate
                :page-count="rows"
                :click-handler="getEmployees"
                :prev-text="'Prev'" :next-text="'Next'"
            ></Paginate>
        </div> 
    </div>
    <Modal :id="'modal'" :title="title">
        <div class="modal-body">
            <form @submit.prevent="save">
                <div class="input-group mb-3">
                    <span class="input-group-text">
                        <i class="fa-solid fa-user"></i>
                    </span>
                    <input v-model="form.name" type="text" placeholder="Name" class="form-control" autofocus
                    ref="nameInput" required>
                </div>
                <div class="input-group mb-3">
                    <span class="input-group-text">
                        <i class="fa-solid fa-at"></i>
                    </span>
                    <input v-model="form.email" type="text" placeholder="Email" class="form-control" 
                    required>
                </div>
                <div class="input-group mb-3">
                    <span class="input-group-text">
                        <i class="fa-solid fa-phone"></i>
                    </span>
                    <input v-model="form.phone" type="text" placeholder="Phone" class="form-control" 
                    required>
                </div>
                <div class="input-group mb-3">
                    <span class="input-group-text">
                        <i class="fa-solid fa-building"></i>
                    </span>
                    <SelectInput v-model="form.department_id" :options="departments" class="form-control" required />
                </div>
                <div class="d-grid col-10 mx-auto">
                <button class="btn btn-success btn-block"  @click="$event=>save">
                    <i class="fa-solid fa-floppy-disk"></i> Add
                </button>
            </div>
            </form>
        </div>
        <div class="modal-footer">
            <button class="btn btn-dark" ref="close" data-dismiss="modal">Close</button>
        </div>
    </Modal>

</template>