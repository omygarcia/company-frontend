<script setup>
    import axios from 'axios';
    import { useAuthStore } from '@/stores/auth';
    import { ref, onMounted } from 'vue';
    import { Bar, Pie } from 'vue-chartjs';
    import { Chart as CharJS, Title,Tooltip,Legend, BarElement, CategoryScale,
        LinearScale, ArcElement
     } from 'chart.js';

     CharJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale,
        ArcElement
     );
    
    const authStore = useAuthStore();
    axios.defaults.headers.common['Authorization'] = 'Bearer '+authStore.authToken;

    const departments = ref([]);
    const employees = ref([]);
    const chartData = ref([]);
    const chartOptions = ref([]);
    const colors = ref([]);
    const loaded = ref(false);

    const random = ()=>{
        return Math.floor(Math.random()*256);
    }

    onMounted(async()=>{
        try
        {

            const info = await axios.get('/api/employeesbydepartment');
            console.log(info)
            info.data.map((row)=>{
                departments.value.push(row.name);
                employees.value.push(row.count);
                colors.value.push("rgb("+random()+","+random()+","+random()+")");
            });
            chartOptions.value = {responsive:true};
            chartData.value = {
                labels:departments.value,
                datasets:[
                    {label:'Employees',data:employees.value,backgroundColor:colors}
                ]
            };
            loaded.value = true;
        }
        catch(error)
        {
            console.log(error)
        }
    });
</script>
<template>
    <div class="row">
        <div class="col-md-8 offset-md-2">
            <Bar
                v-if="loaded"
                id="my-chart-id"
                :options="chartOptions"
                :data="chartData"
            />
        </div>
        <div class="col-md-6 offset-md-2">
            <Pie
                v-if="loaded"
                id="my-chart-id"
                :options="chartOptions"
                :data="chartData"
            />
        </div>
    </div>
</template>