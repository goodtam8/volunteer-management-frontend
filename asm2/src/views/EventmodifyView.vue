<script setup>
import { ref, onMounted, computed,nextTick, watch } from 'vue'
import { useRoute, useRouter } from "vue-router";
import { jwtDecode } from "jwt-decode";

const router = useRouter();
const token = ref(localStorage.getItem('token'))
const decoded = ref("");
const num=ref("");
const role = ref("");

const route=useRoute();
const event=ref({

title: '',
organizer: '',
dateTime: '06/12/2024,13:00',
quota: 10,
location:'Apartment,studio or floor',
image:'https//image.com/example.png',

highlight: false,
description:''
})

const SubmitEvent=async function(){

    var url='/api/event';
    url = url + '/' + event.value._id;
    var method='PUT';


    const response = await fetch(url, {
        method: method,
        headers: {
            'Content-Type': 'application/json',
            Authorization: `Bearer ${token.value}`

        },
        body: JSON.stringify(event.value)
    });
    // convert the response to json
    const json = await response.json();
    // log the json
    console.log(json);
    // alert the user
    alert(JSON.stringify(json));
}

// a function to get the booking from the backend
const getEvent = async function () {
    alert("hgh");
    // get the booking from the backend
    const response = await fetch('/api/event/' + route.params.id);
    // convert the response to json
    const json = await response.json();
    // log the json
    console.log(json);
    // set the booking, copy by value instead of reference
    event.value = json.result;
    // Wait for the change to get flushed to the DOM
    await nextTick();
    // set the superhero


}

onMounted(async () => {
    // if there is an id in the route
    if (token.value) {
        decoded.value = jwtDecode(token.value);
        console.log(decoded.value);
        role.value = decoded.value.role;
        num.value=decoded.value._id
    }
    if (route.params.id) {
        getEvent();
    }
   
});
const deleteEvent = async function () {
    // post the booking to the backend
    const response = await fetch('/api/event/' + event.value._id, {
        method: 'DELETE',
        headers: {
            Authorization: `Bearer ${num.value}`

        }
    });
    // convert the response to json
    const json = await response.json();
    // log the json
    console.log(json);
    // alert the user
    alert(JSON.stringify(json));
    // redirect to the home page
    router.push('/');
}








</script>


<template>

<nav aria-label="breadcrumb">
        <ol class="breadcrumb">
            <li class="breadcrumb-item"><a href="http://localhost:5173/">Home</a></li>
            <li class="breadcrumb-item"><a href="http://localhost:5173/event">Events</a></li>
            <li class="breadcrumb-item active" aria-current="page">Edit Event</li>
        </ol>
    </nav>
    <br>


    <form class="container"  @submit.prevent="SubmitEvent">
        <div class="col">
         <button type="button" class="btn btn-danger"  v-on:click="deleteEvent">Delete </button>


        </div>


        <div class="row">
            <div class="col">
                <div class="row mb-3">
                    <label for="inputtitle3" class="col-sm-2 col-form-label">Event Title</label>
                    <div class="row mb-3">
                        <input type="text" class="form-control" name="title" id="inputtitle3" v-model=event.title required>
                    </div>
                </div>
                <div class="row mb-3">
                    <label for="inputOrganizer3" class="col-sm-2 col-form-label">Organizer</label>
                    <div class="row mb-3">
                        <input type="text" class="form-control" name="organizer" id="inputOrganizer3" v-model=event.organizer required>
                    </div>
                </div>
            </div>
            <div class="col">
                <div class="mb-3">
                    <label for="exampleFormControlTextarea1" class="form-label">description</label>
                    <textarea class="form-control row mb-4" id="exampleFormControlTextarea1" name="description" rows="7" v-model=event.description>
                    
                    </textarea>
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col">
                <div class="row mb-3">
                    <label for="inputDatetime3" class="col-sm-2 col-form-label">Datetime</label>
                    <div class="row mb-3">

                        <input type="text" class="form-control" name="datetime" id="inputDatetime3"
                        v-model=event.dateTime required>
                    </div>

                </div>
            </div>

            <div class="col">
                <div class="row mb-3">
                    <label for="inputQuota3" class="col-sm-2 col-form-label">Quota</label>
                    <div class="row mb-3">
                        <input type="number" class="form-control" name="quota" id="inputQuota3" v-model=event.quota min=1 required>
                    </div>
                </div>
            </div>

        </div>


        <div class="row">
            <div class="col">
                <div class="row mb-3">
                    <label for="inputLocation3" class="col-sm-2 col-form-label">Location</label>
                    <div class="row mb-3">

                        <input type="text" class="form-control" name="location" id="inputLocation3"
                        v-model="event.location" required>
                    </div>

                </div>
            </div>

            <div class="col">
                <div class="row mb-3">
                    <label for="inputImage" class="col-sm-2 col-form-label">Image</label>
                    <div class="row mb-3">
                        <input type="url" class="form-control" name="image" id="inputImage"
                        v-model=event.image required>
                    </div>
                </div>
            </div>
        </div>


<div class="row">
        <div class="col">
        <div class="col-sm-3">
            <div class="row mb-4 ">
                <div class="form-check">
                    <input class="form-check-input" name="highlight" type="checkbox" id="gridCheck1" v-model=event.highlight>
                    <label class="form-check-label" for="gridCheck1">
                        Highlight
                    </label>
                </div>
            </div>
        </div>
    </div>
    <div class="col">
        <button type="submit" class="btn btn-primary btn-lg float-end" >
            Save </button>

        </div>
    </div>



    </form>



</template>