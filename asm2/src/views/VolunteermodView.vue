<script setup>
import { ref, onMounted, computed,nextTick, watch } from 'vue'
import { useRoute, useRouter } from "vue-router";
import { jwtDecode } from "jwt-decode";
const token = ref(localStorage.getItem('token'))
const decoded = ref("");
const num=ref("");
const loading = ref(false);

const router = useRouter();
const route=useRoute();
const role = ref("");
const list=ref([]);
const total = ref(0);

const register = ref({
    email: '',
    password: '',
    name: ' ',
    contact: '',
    description: '',
    terms: false,
    age:''
})

const deleteVolunteer = async function (id) {
    // post the booking to the backend
    const response = await fetch('/api/event/' +id+'/'+register.value._id+'/remove', {
        
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
    // redirect to the home page
    location.reload();
}
const submitUpdate=async function(){

    var url='/api/enroll';
    url = url + '/' + register.value._id;
    var method='PUT';


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
    alert(role.value)
    // get the booking from the backend
    const response = await fetch('/api/enroll/' + route.params.id);
    // convert the response to json
    const json = await response.json();
    // log the json
    console.log(json.result2);
    // set the booking, copy by value instead of reference
    register.value = json.result;
    list.value=json.result2;

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
        num.value=decoded.value._id
    }
    // if there is an id in the route
    if (route.params.id) {
        getVolunteer();
    }
   
});








</script>



<template>



    <nav aria-label="breadcrumb">
        <ol class="breadcrumb">
            <li class="breadcrumb-itetm"><a href="http://localhost:5173/">Home</a></li>
            <li class="breadcrumb-item active" aria-current="page">/Volunteer</li>
        </ol>
    </nav>
    <br>
    <form class="container" @submit.prevent="submitUpdate" >

        <div class="col">
         <button type="button" class="btn btn-danger"  v-on:click="deleteVolunteer">Delete </button>


        </div>
        <div class="row">
            <div class="col">
                <div class="row mb-3">
                    <label for="inputEmail3" class="col-sm-2 col-form-label">Email</label>
                    <div class="row mb-3">
                        <input type="email" class="form-control" name="email" id="inputEmail3" required v-model=register.email>
                    </div>
                </div>
                <div class="row mb-3">
                    <label for="inputPassword3" class="col-sm-2 col-form-label">Password</label>
                    <div class="row mb-3">
                        <input type="password" class="form-control" name="password" id="inputPassword3" required v-model=register.password>
                    </div>
                </div>
                <div class="row mb-3">
                    <label for="inputName3" class="col-sm-2 col-form-label">Name</label>
                    <div class="row mb-3">
                        <input type="text" class="form-control" name="name" id="inputName3" required v-model=register.name>
                    </div>
                </div>
                <div class="row mb-3">
                    <label for="inputContact3" class="col-sm-2 col-form-label">Contact</label>
                    <div class="row mb-3">
                        <input type="tel" class="form-control" name="contact" id="inputContact3" required v-model=register.contact>
                    </div>
                </div>
                <label class="col-sm-2 col-form-label">Age Group</label>
                <select class="form-select row mb-4" aria-label="Default select example" required v-model=register.age>
                    <option selected>Dropdown</option>
                    <option value="10-18">10-18</option>
                    <option value="19-25">19-25</option>
                    <option value="26-40">26-40</option>
                    <option value="41-65">41-65</option>
                    <option value="65+">>65</option>

                </select>
                <div class="mb-3">
                    <label for="exampleFormControlTextarea1" class="form-label">About me and remark</label>
                    <textarea class="form-control row mb-4" id="exampleFormControlTextarea1" name="description"
                        rows="3" v-model=register.description></textarea>
                </div>
                <div class="col-sm-3">
                    <div class="row mb-4 ">
                        <div class="form-check">
                            <input class="form-check-input" name="terms" type="checkbox" id="gridCheck1" required v-model=register.terms>
                            <label class="form-check-label" for="gridCheck1">
                                Agree to Terms and Conditions
                            </label>
                        </div>
                    </div>
                </div>
            </div>


            <div class="col">
                <section>
    <o-table
        :data="list"
        
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
        @sort="onSort">
        
        >
        
        <o-table-column
            v-slot="props"
            field="title"
            label="Event Title"
            sortable>


          {{ props.row.title }}
        </o-table-column>
        <o-table-column v-slot="props" label="Action" width="500">

        <a :href="`/event/edit/${props.row._id}`" class="btn btn-secondary " tabindex="-1" role="button" >Edit</a>
            {{ props.row._id }}

        <a class="btn btn-danger " tabindex="-1" role="button" @click="deleteVolunteer(props.row._id)" >Delete</a>
            {{ props.row._id }}

        
        
        </o-table-column>
    </o-table>
</section>

            </div>
        </div>

        
            <button type="submit" class="btn btn-primary">Register</button>



    </form>







</template>

