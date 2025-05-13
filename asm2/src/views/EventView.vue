<script setup>
import { ref, onMounted, computed,nextTick, watch } from 'vue'
import { useRoute, useRouter } from "vue-router";
import { jwtDecode } from "jwt-decode";

const router = useRouter();
const route=useRoute();
const token = ref(localStorage.getItem('token'))
const decoded = ref("");
const role = ref("");

const event=ref({

title: '',
Organizer: '',
dateTime: '06/12/2024,13:00',
quota: 10,
location:'Apartment,studio or floor',
image:'https//image.com/example.png',

highlight: false,
description:'',
list:[]
})
const data = ref([]);
const volunteer = ref([]);
const num=ref("");



const getEvent = async function () {
    // get the booking from the backend
    const response = await fetch('/api/event/' + route.params.id);
    // convert the response to json
    const json = await response.json();
    // log the json
    console.log(json.result2);
    // set the booking, copy by value instead of reference
    event.value = json.result ;
    if (json.result2.length>0) {
    volunteer.value=json.result2[0].volunteers;
    }
    alert(JSON.stringify(volunteer.value))

    // Wait for the change to get flushed to the DOM
    await nextTick();
    // set the superhero


}

const deleteVolunteer = async function (id) {
    // post the booking to the backend
    const response = await fetch('/api/event/' + event.value._id+'/'+id+'/remove', {
        
        headers:{
        Authorization: `Bearer ${token.value}`
    },
    //加返page ge 野 lab6 ge 野
        method: 'PATCH'
    });
    // convert the response to json
    const json = await response.json();
    // log the json
    console.log(json);
    // alert the user
    alert(JSON.stringify(json));
    // redirect to the home page
    location.reload();
}
onMounted(async () => {
    if (token.value) {
        decoded.value = jwtDecode(token.value);
        console.log(decoded.value);
        role.value = decoded.value.role;
        num.value=decoded.value._id
    }
    // if there is an id in the route
    if (route.params.id) {
        getEvent();
    }
   
});




</script>
<template>
    <nav aria-label="breadcrumb">
        <ol class="breadcrumb">
            <li class="breadcrumb-item"><a href="http://localhost:5173/">Home</a></li>
            <li class="breadcrumb-item"><a href="http://localhost:5173/event">Events</a></li>
            <li class="breadcrumb-item active" aria-current="page">Event Title</li>
        </ol>
    </nav>
    <br>
 <div class="row row-cols-1 row-cols-md-2 col-sm-6">
        <div class="card " style="width: 25rem;">
            <div class="card-body">
                <h5 class="card-title">
                    {{ event.title }}                </h5>
                <h6 class="card-subtitle mb-2 text-body-secondary">
                    {{ event.Organizer }}                 </h6>
                <p class="card-text">
                    {{ event.description }}                 </p>
                <ul class="list-group">
                    <li class="list-group-item">
                        {{ event.dateTime }}                     </li>
                    <li class="list-group-item">
                        {{ event.location }}                     </li>
                    <li class="list-group-item">
                        {{ event.quota }}                     </li>
                </ul>
            </div>
        </div>


    <div class="col mb-2">
        <div class="card w-40 ">
            <img src="https://s.yimg.com/ny/api/res/1.2/6I4vEaEYfbjsPX.IX13thA--/YXBwaWQ9aGlnaGxhbmRlcjt3PTk2MDtoPTU0MDtjZj13ZWJw/https://media.zenfs.com/zh-tw/game_nownews_com_902/4249e135bfeb536372a5865267714b09" class="wcard-img-top" alt="..."
               style="  align-items: center">
            <div class="card-body">
               
                <section>
    <o-table
        :data="volunteer"
        :loading="loading"
        paginated
        backend-pagination
        :total="total"
        :per-page="perPage"
        aria-next-label="Next page"
        aria-previous-label="Previous page"
        aria-page-label="Page"
        aria-current-label="Current page"
        backend-sorting
        :default-sort-direction="defaultSortOrder"
        :default-sort="[sortField, sortOrder]"
        @page-change="onPageChange"
        @sort="onSort"  v-if="role == 'admin'">
        
        <o-table-column
            v-slot="props"
            field="name"
            label="Volunteer name"
            sortable>


          {{ props.row.name }}
        </o-table-column>
        <o-table-column
            v-slot="props"
            field="contact"
            label="Contact"
            numeric
            sortable>
            {{ props.row.contact }}
        </o-table-column>
        
        <o-table-column v-slot="props" label="Action" width="500">
            <a :href="`/volunteer/${props.row._id}`" class="btn btn-secondary " tabindex="-1" role="button" >Edit</a>
            {{ props.row._id }}

            <a class="btn btn-danger " tabindex="-1" role="button" @click="deleteVolunteer(props.row._id)" >Delete</a>
            {{ props.row._id }}

        </o-table-column>


    </o-table>

    <p class="fw-bold" v-if="role != 'admin'">Become a volunteer!</p>
    <p class="card-text" v-if="role != 'admin'">You time and talent can make a real different in people's life.</p>
</section>

            </div>
        </div>
    </div></div>



</template>