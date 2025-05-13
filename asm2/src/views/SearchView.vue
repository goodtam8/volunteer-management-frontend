<script setup>

import { ref, onMounted, computed } from "vue" // add watch
import { RouterLink, useRoute } from "vue-router";
const route = useRoute();

const total = ref(0);
const loading = ref(false);

const page = ref(1);
const perPage = ref(20);
const events = ref([])
const ge=ref(88);

const loadAsyncData = () => {
const params = [
        // "api_key=bb6f51bef07465653c3e553d6ab161a8",
        // "language=en-US",
        // "include_adult=false",
        // "include_video=false",
        `title=${route.params.keyword}`,
    ].join("&");
    fetch(`/api/event/?${params}`)//加返page ge 野 lab6 ge 野
        .then((response) => response.json())
        .then((result) => {
           
            events.value = result.event;
            perPage.value = result.perPage;
            total.value = result.total;           
            loading.value = false;
            ge.value=result.page;
        })

    }
onMounted(async () => {
    // if there is an id in the route
    loadAsyncData();

   
});

const pa=computed(()=>{

var a =[]

for (let i = 1; i <= Math.ceil(total.value / perPage.value); i++) {
    // if the page is the current page
   
       a.push(i);
    }
    return a;
});

</script>


<template>
<nav aria-label="breadcrumb">
        <ol class="breadcrumb">
            <li class="breadcrumb-item"><a href="http://localhost:5173/">Home</a></li>
            <li class="breadcrumb-item active" aria-current="page">Search</li>
        </ol>
    </nav>
    <br>


<div class="row" >


<div class="card col-md-4"  v-for="ev in events" :key=ev.id>

    <RouterLink :to="`/event/detail/${ev._id}`" ></RouterLink><!---//router link -->

    <img :src="`${ev.image}`" class="card-img-top"  :key="ev.image"  alt="...">
    
    <div class="card-body">
        <h5 class="card-title" :key="ev.title" :value="ev.title">
            {{ev.title}}
        </h5>
        <p class="card-text"  :key="ev.description" :value="ev.description">
            {{ev.description}}

        </p>
        <div class="row">
            <div class="col">
                <p class="card-text"><small class="text-body-secondary">Last updated 3 mins
                        ago</small></p>
            </div>
            <div class="col">
                <RouterLink :to="`/event/edit/${ev._id}`"  class=" btn btn-primary btn-lg float-end" >Edit</RouterLink>
            </div>
        </div>
    </div>
</div>


</div>






<br>


<nav aria-label="Page navigation example"  >
    
    <ul class="pagination">
                        <li class="page-item" v-for='ea in pa' :key="ea"><a class="page-link" :href="`/event/page/${ea}`">{{ ea }}</a></li>


                       
                    <!-- v-if="page.value==ea" -->

    </ul>
</nav>
<br>
</template>