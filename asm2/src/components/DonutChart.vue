<script setup>


import { ref, onMounted } from "vue";
const token = ref(localStorage.getItem('token'))
import { jwtDecode } from "jwt-decode";

const decoded = ref("");
const num=ref("");
const role = ref("");
const options = ref({});
const series = ref([]);

onMounted(async () => {
    if (token.value) {
        decoded.value = jwtDecode(token.value);
        console.log(decoded.value);
        role.value = decoded.value.role;
        num.value=decoded.value._id
    }
    var response = await fetch("/api/enroll/stats/organizer", {
        headers: {
            Authorization: `Bearer ${token.value}`
        },
    })
    var json = await response.json();
    options.value = { labels: json.map((item) => item._id) };

    series.value = json.map((item) => item.total);
});


</script>

<template>
    <div>
        <apexchart type="donut" :options="options" :series="series" />
    </div>
</template>