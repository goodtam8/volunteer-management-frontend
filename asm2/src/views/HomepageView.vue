<script setup>
import { ref, onMounted, computed } from "vue" // add watch
import { RouterLink, useRoute } from "vue-router";// how to send two use route
import { differenceInMinutes } from 'date-fns'
import { jwtDecode } from "jwt-decode";

//const page = ref();
const perPage = ref(6);
const total = ref(0);
const loading = ref(false);
const ge = ref(88);

const sortField = ref("modifiedAt");
const sortOrder = ref("desc");
const defaultSortOrder = ref("desc");
const events = ref([])
const events2=ref([])
const role = ref("");
const token = ref(localStorage.getItem('token'))
const decoded = ref("");
const num=ref("");

const loadAsyncData2 = () => {

  const params = [
    // "api_key=bb6f51bef07465653c3e553d6ab161a8",
    // "language=en-US",
    // "include_adult=false",
    // "include_video=false",
    `highlight=true`,

  ].join("&");


  fetch(`/api/event?${params}`)//都係pagination 問題
    .then((response) => response.json())
    .then((result) => {

      events2.value = result.event;
      total.value = result.total;
      loading.value = false;
      ge.value = result.page;
    })
};



const loadAsyncData = () => {
  const params = [
    // "api_key=bb6f51bef07465653c3e553d6ab161a8",
    // "language=en-US",
    // "include_adult=false",
    // "include_video=false",
    `sort_by=${sortField.value}.${sortOrder.value}`,
    `perPage=${3}`,

  ].join("&");


  fetch(`/api/event?${params}`)//都係pagination 問題
    .then((response) => response.json())
    .then((result) => {

      events.value = result.event;
      perPage.value = result.perPage;
      total.value = result.total;
      loading.value = false;
      ge.value = result.page;
    })
};


onMounted(() => {
  if (token.value) {
        decoded.value = jwtDecode(token.value);
        console.log(decoded.value);
        role.value = decoded.value.role;
        num.value=decoded.value._id
    }
  loadAsyncData();
  loadAsyncData2();
});


</script>

<template>
  <div id="carouselExample" class="carousel slide">
    <div class="carousel-inner">
      <div class="carousel-item" v-for="ev in events2" :key="ev._id"  :class="{active: ev._id== events2[1]._id}">
        <img :src="ev.image" class="d-block w-100" alt="...">
      </div>
      
    </div>
    <button class="carousel-control-prev" type="button" data-bs-target="#carouselExample" data-bs-slide="prev">
      <span class="carousel-control-prev-icon" aria-hidden="true"></span>
      <span class="visually-hidden">Previous</span>
    </button>
    <button class="carousel-control-next" type="button" data-bs-target="#carouselExample" data-bs-slide="next">
      <span class="carousel-control-next-icon" aria-hidden="true"></span>
      <span class="visually-hidden">Next</span>
    </button>
  </div>





  
  <br>


  <ul class="nav nav-tabs">
    <li class="nav-item">
      <a class="nav-link active" aria-current="page" href="#">Recent</a>
    </li>

  </ul>


  <div class="row">


    <div class="card col-md-4" backend-sorting :default-sort-direction="defaultSortOrder"
      :default-sort="[sortField, sortOrder]" @sort="onSort" v-for="ev in events" :key=ev.id>
      <RouterLink :to="`/event/detail/${ev._id}`"></RouterLink><!---//router link -->
      <img :src="`${ev.image}`" class=" card-img-top" alt="...">
      <div class="card-body">
        <h5 class="card-title">
          {{ ev.title }}
        </h5>
        <p class="card-text">
          {{ ev.description }}
        </p>


        <div class="row">
          <div class="col">
            <p class="card-text"><small class="text-body-secondary" field="modifiedAt" label="modifiedAt" sortable
                centered>                    <p>Last Modified: {{ differenceInMinutes(new Date(), new Date(ev.modifiedAt)) }} minutes ago.</p></small></p>
          </div>

                    <div class="col" v-if="role == 'volunteer'&& (ev.list||[]).indexOf(num)==-1">
                        <a class=" btn btn-primary btn-lg float-end" @click="join(ev._id)">Join
                        </a>
                    </div>

        </div>


      </div>
    </div>


  </div>
</template>