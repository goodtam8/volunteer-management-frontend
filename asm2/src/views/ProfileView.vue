<script setup>
import { ref, onMounted, computed, nextTick, watch } from 'vue'
import { useRoute, useRouter } from "vue-router";
import { jwtDecode } from "jwt-decode";
import DonutChart from '../components/DonutChart.vue'
import { differenceInMinutes } from 'date-fns'

const token = ref(localStorage.getItem('token'))
const decoded = ref("");
const num = ref("");
const loading = ref(false);
//const page = ref();
const perPage2= ref(6);
const total2 = ref(0);
const router = useRouter();
const route = useRoute();
const role = ref("");
const list = ref([]);
const ge = ref(88);

const register = ref({
    email: '',
    password: '',
    name: ' ',
    contact: '',
    description: '',
    terms: false,
    age: ''
})

const submitUpdate = async function () {

    var url = '/api/enroll';
    url = url + '/' + register.value._id;
    var method = 'PUT';


    const response = await fetch(url, {
        method: method,
        headers: {
            'Content-Type': 'application/json',
            Authorization: `Bearer ${token.value}`

        },
        body: JSON.stringify(register.value)
    });
    // convert the response to json
    const json = await response.json();
    // log the json
    console.log(json);
    // alert the user
}


// a function to get the booking from the backend
const getVolunteer = async function () {
    // get the booking from the backend
    const response = await fetch('/api/enroll/' + num.value,{
        
        headers:{
        Authorization: `Bearer ${token.value}`
    }});
    // convert the response to json
    const json = await response.json();
    // log the json
    console.log(json.result2);
    // set the booking, copy by value instead of reference
    register.value = json.result;
    list.value = json.result2;
          


    console.log(list.value);

    // Wait for the change to get flushed to the DOM
    // set the superhero


}

onMounted(async () => {
    if (token.value) {
        console.log(register.value);
        console.log(list.value);
        decoded.value = jwtDecode(token.value);
        console.log(decoded.value);
        role.value = decoded.value.role;
        num.value = decoded.value._id
    }
    // if there is an id in the route
    if (num.value) {
        getVolunteer();
    }

});


const pa = computed(() => {

var a = []

for (let i = 1; i <= Math.ceil(total.value / perPage.value); i++) {
    // if the page is the current page

    a.push(i);
}
return a;
});

const onPageChange = (p) => {
page.value = p;
getVolunteer();
};






</script>



<template>
    <nav aria-label="breadcrumb">
        <ol class="breadcrumb">
            <li class="breadcrumb-itetm"><a href="http://localhost:5173/">Home</a></li>
            <li class="breadcrumb-item active" aria-current="page">/My Event</li>
        </ol>
    </nav>
    <br>

    <div class="row">

        <div class="col-12 col-lg-6">
            <div class="card h-100">

                <form class="container" @submit.prevent="submitUpdate">


                    <div class="row gx-5">

                        <div class="col-12 col-lg-6">
                            <label for="inputEmail3" class="col-sm-2 col-form-label">Email</label>
                            <div class="row mb-3">
                                <input type="email" class="form-control" name="email" id="inputEmail3" required
                                    v-model=register.email>
                            </div>
                        </div>

                        <div class="col-12 col-lg-6">
                            <label for="inputPassword3" class="col-sm-2 col-form-label">Password</label>
                            <div class="row mb-3">
                                <input type="password" class="form-control" name="password" id="inputPassword3"
                                    v-model=register.password>
                            </div>
                        </div>
                    </div>
                    <div class="row gx-5">

                        <div class="col-12 col-lg-6">
                            <label for="inputName3" class="col-sm-2 col-form-label">Name</label>
                            <div class="row mb-3">
                                <input type="text" class="form-control" name="name" id="inputName3" v-model=register.name>
                            </div>
                        </div>
                        <div class="col-12 col-lg-6">
                            <label for="inputContact3" class="col-sm-2 col-form-label">Contact</label>
                            <div class="row mb-3">
                                <input type="tel" class="form-control" name="contact" id="inputContact3"
                                    v-model=register.contact>
                            </div>
                        </div>
                    </div>



                    <div class="row gx-5">
                        <div class="col-12 col-lg-6">

                            <label class="col-sm-2 col-form-label">Age Group</label>
                            <select class="form-select row mb-4" aria-label="Default select example" v-model=register.age>
                                <option selected>Dropdown</option>
                                <option value="10-18">10-18</option>
                                <option value="19-25">19-25</option>
                                <option value="26-40">26-40</option>
                                <option value="41-65">41-65</option>
                                <option value="65+">>65</option>

                            </select>
                        </div>


                        <div class="col-12 col-lg-6">
                            <label for="exampleFormControlTextarea1" class="form-label">About me and remark</label>
                            <textarea class="form-control row mb-4" id="exampleFormControlTextarea1" name="description"
                                rows="3" v-model=register.description></textarea>
                        </div>
                    </div>
                    <button type="submit" class="btn btn-primary">Save</button>
                </form>

            </div>
        </div>

        <div class="col-12 col-lg-6">
            <div class="card h-100">
                <h5 class="card-title">Event Organizer</h5>

                <DonutChart />
            </div>

        </div>

    </div>


    <div class="row">

        <div class="card col-md-4" v-for="ev in list" :key=ev.id>

            <RouterLink :to="`/event/detail/${ev._id}`"></RouterLink><!---//router link -->

            <img :src="`${ev.image}`" class="card-img-top" :key="ev.image" alt="...">

            <div class="card-body">
                <RouterLink :to="`/event/detail/${ev._id}`" h5 class="card-title" :key="ev.title" :value="ev.title">
                    {{ ev.title }}

                </RouterLink>
                <p class="card-text" :key="ev.description" :value="ev.description">
                    {{ ev.description }}

                </p>
                <div class="row">
                    <div class="col">
                        <p>Last Modified: {{ differenceInMinutes(new Date(), new Date(ev.modifiedAt)) }} minutes ago.</p>

                    </div>

                </div>
            </div>
        </div>









    </div>

</template>


