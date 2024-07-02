<script setup>
    import { ref, onMounted } from 'vue';
    import axios from 'axios';
    import { useAuthStore } from '@/stores/auth';
    import SelectInput from '@/components/SelectInput.vue';
    import { DataTable } from 'datatables.net-vue3';
    //import 'datatables.net-dt/css/jquery.dataTables.css';
    import 'datatables.net-dt/css/dataTables.dataTables.css'
    import ButtonsHtml5 from 'datatables.net-buttons/js/buttons.html5';
    import 'datatables.net-buttons/js/buttons.print';
    import 'datatables.net-responsive-dt';
    import 'datatables.net-responsive-dt/css/responsive.dataTables.min.css';
    import JSZip from 'jszip';
    import pdfmake from 'pdfmake/build/pdfmake';
    //import * as pdfFonts from 'pdfmake/build/vfs_fonts';
    //pdfmake.vfs = pdfFonts.pdfmake?pdfFonts.pdfMake.vfs:pdfmake.vfs;

    import pdfFonts from "pdfmake/build/vfs_fonts";
    pdfMake.vfs = pdfFonts.pdfMake.vfs;

    window.JSZip = JSZip;
    DataTable.use(ButtonsHtml5);
    const authStore = useAuthStore();
    axios.defaults.headers.common['Authorization'] = 'Bearer '+authStore.authToken;

    const departments = ref([]);
    const employees = ref([]);
    const colums1 = ref([]);
    const colums2 = ref([]);
    const buttons1 = ref([]);
    const buttons2 = ref([]);
    const report = ref('1');
    const types = ref([{'id':'1','name':'Employees'},{'id':'2','name':'Departments'}]);

    onMounted(async()=>{
        const d = await axios.get('/api/departments');
        departments.value = d.data;
        const e = await axios.get('/api/employeesall');
        //console.log(e.data)
        employees.value = e.data;
    });

    colums1.value = [{data:null,render:function(data,type,row,meta){
        return (meta.row+1);
    }},
    {data:'name'},
    {data:'email'},
    {data:'phone'},
    {data:'department'},
    ];

    colums2.value = [{data:null,render:function(data,type,row,meta){
        return (meta.row + 1)
    }},
    {data:'name'}
    ];

    buttons1.value = [
        {
            title:'employees',
            extend:'excelHtml5',
            text:'<i class="fa-solid fa-file-excel"></i> Excel',
            className:'btn btn-success'
        },
        {
            title:'employees',
            extend:'pdfHtml5',
            text:'<i class="fa-solid fa-file-pdf"></i> PDF',
            className:'btn btn-danger'
        },
        {
            title:'employees',
            extend:'print',
            text:'<i class="fa-solid fa-print"></i> PRINT',
            className:'btn btn-dark'
        },
        {
            title:'employees',
            extend:'copy',
            text:'<i class="fa-solid fa-copy"></i> COPY',
            className:'btn btn-secondary'
        },
    ];

    buttons2.value = [
        {
            title:'departments',
            extend:'excelHtml5',
            text:'<i class="fa-solid fa-file-excel"></i> Excel',
            className:'btn btn-success'
        },
        {
            title:'departments',
            extend:'pdfHtml5',
            text:'<i class="fa-solid fa-file-pdf"></i> PDF',
            className:'btn btn-danger'
        },
        {
            title:'departments',
            extend:'print',
            text:'<i class="fa-solid fa-print"></i> PRINT',
            className:'btn btn-dark'
        },
        {
            title:'departments',
            extend:'copy',
            text:'<i class="fa-solid fa-copy"></i> COPY',
            className:'btn btn-secondary'
        },
    ];
</script>
<template>
    <div class="row mb-5">
        <div class="col-md-8 offset-md-2">
            Report:
            <SelectInput id="rep" class="mt-1" v-model="report" :options="types"></SelectInput>
        </div>
    </div>
    <div class="row" v-if="report=='1'">
        <div class="col-md-8 offset-md-2">
            <div class="table-responsive">
                <DataTable :data="employees" :columns="colums1"
                class="table table-striped display"
                :options="{responsive:true,autoWidth:false,dom:'Bfrtip',buttons:buttons1}"
                >
                    <thead class="bg-success text-white">
                        <tr>
                            <th>#</th>
                            <th>NAME</th>
                            <th>EMAIL</th>
                            <th>PHONE</th>
                            <th>DEPARTMENT</th>
                        </tr>
                    </thead>
                </DataTable>
            </div>
        </div>
    </div>
    <div class="row" v-else>
        <div class="col-md-8 offset-md-2">
            <div class="table-responsive">
                <DataTable :data="departments" :columns="colums2"
                class="table table-striped"
                :options="{responsive:true,autoWidth:false,dom:'Bfrtip',buttons:buttons2}"
                >
                <thead class="bg-success text-white">
                    <tr>
                        <th>#</th>
                        <th>NAME</th>
                    </tr>
                </thead>
                </DataTable>
            </div>
        </div>
    </div>
</template>