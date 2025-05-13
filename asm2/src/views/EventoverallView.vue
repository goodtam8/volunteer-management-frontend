<script setup>
import { ref, onMounted, computed } from "vue" // add watch
import { RouterLink } from "vue-router";
import { jwtDecode } from "jwt-decode";
import { differenceInMinutes } from 'date-fns'
const page = ref();
const perPage = ref(6);
const data = ref([]);
const total = ref(0);
const loading = ref(false);
const ge=ref(88);
const num=ref("");

const events = ref([])
const pa=computed(()=>{

    var a =[]

    for (let i = 1; i <= Math.ceil(total.value / perPage.value); i++) {
        // if the page is the current page
       
           a.push(i);
        }
        return a;
    });
const role = ref("");
const token = ref(localStorage.getItem('token'))
const decoded = ref("");





const loadAsyncData = () => {
   if(token.value){
    fetch('/api/event/',{
        headers:{
        Authorization: `Bearer ${token.value}`
    }

        
    })//加返page ge 野 lab6 ge 野
    
        .then((response) => response.json())
        .then((result) => {
           
            events.value = result.event;
            perPage.value = result.perPage;
            total.value = result.total;           
            loading.value = false;
            ge.value=result.page;
        })
}else{
    fetch('/api/event/')//加返page ge 野 lab6 ge 野
    
        .then((response) => response.json())
        .then((result) => {
           
            events.value = result.event;
            perPage.value = result.perPage;
            total.value = result.total;           
            loading.value = false;
            ge.value=result.page;
        })



}



};


onMounted(() => {
    if (token.value) {
        decoded.value = jwtDecode(token.value);
        console.log(decoded);
        num.value=decoded.value._id
        role.value = decoded.value.role;
    }
    loadAsyncData();
});

const join=async function(id){


const response = await fetch(`/api/event/${id}/manage`, {
    method: 'PATCH',
    headers:{
        Authorization: `Bearer ${token.value}`
    }
});
// convert the response to json
const json = await response.json();
// log the json
console.log(json);
// alert the user
alert(JSON.stringify(json));
location.reload();

// redirect to the home page
}
</script>

<template>
    <div class="row">
        <div class="col">
            <nav aria-label="breadcrumb">
                <ol class="breadcrumb">
                    <li class="breadcrumb-item"><a href="http://localhost:5173/">Home</a></li>
                    <li class="breadcrumb-item active" aria-current="page">Events</li>
                </ol>
            </nav>
        </div>

        <div class="col" v-if="role == 'admin'">
            <RouterLink :to="`/event/new`" class=" btn btn-primary btn-lg float-end">New
            </RouterLink>
        </div>
    </div>

    <br>


<div class="row" >


    <div class="card col-md-4"  v-for="ev in events" :key=ev.id>


        <img :src="`${ev.image}`" class="card-img-top"  :key="ev.image"  alt="..." >
        
        <div class="card-body">
            
                <RouterLink :to="`/event/detail/${ev._id}`" h5 class="card-title" :key="ev.title" :value="ev.title">
                    {{ev.title}}

                </RouterLink>
            <p class="card-text"  :key="ev.description" :value="ev.description">
                {{ev.description}}

            </p>
            <div class="row">
                <div class="col">
                    <p>Last Modified: {{ differenceInMinutes(new Date(), new Date(ev.modifiedAt)) }} minutes ago.</p>

                </div>
                <div class="col" v-if="role=='admin'">
                    <RouterLink :to="`/event/edit/${ev._id}`"  class=" btn btn-primary btn-lg float-end" >Edit</RouterLink>
                </div>
                <div class="col" v-if="role == 'volunteer'&& (ev.list||[]).indexOf(num)==-1">
                        <a class=" btn btn-primary btn-lg float-end" @click="join(ev._id)">Join
                        </a>
                    </div>

            </div>
        </div>
    </div>

  
</div>






<br>


<nav aria-label="Page navigation example"  >
    
        <ul class="pagination">
                            <li class="page-item" v-for='ea in pa' :key="ea"><a class="page-link" :href="`/event/page/${ea}`" :class="{active: 1== ea}">{{ ea }}</a></li>


                           
                        <!-- v-if="page.value==ea" -->

        </ul>
    </nav>
   
    <br>
</template>