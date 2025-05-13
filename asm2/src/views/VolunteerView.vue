<script setup>
import { ref, onMounted, computed,nextTick, watch } from 'vue'
import { useRoute, useRouter } from "vue-router";
const router = useRouter();
const route=useRoute();


const register = ref({
    email: '',
    password: '',
    name: ' ',
    contact: '',
    description: '',
    terms: false,
    age:''
})
const SubmitVolunteer=async function(){
var url='/api/enroll';
var method='POST';

const response = await fetch(url, {
        method: method,
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify(register.value)
    });
    // convert the response to json
    const json = await response.json();
    // log the json
    console.log(json);
    // alert the user
    alert(JSON.stringify(json));
}  

</script>

<template>



    <nav aria-label="breadcrumb">
        <ol class="breadcrumb">
            <li class="breadcrumb-itetm"><a href="http://localhost:5173/">Home</a></li>
            <li class="breadcrumb-item active" aria-current="page">/Become Volunteer!</li>
        </ol>
    </nav>
    <br>
    <form class="container" @submit.prevent="SubmitVolunteer" >
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
                        <input type="text" class="form-control" name="name" id="inputName3"  required v-model=register.name>
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
                <div class="card mb-3 ">
                    <img src="https://s.yimg.com/ny/api/res/1.2/6I4vEaEYfbjsPX.IX13thA--/YXBwaWQ9aGlnaGxhbmRlcjt3PTk2MDtoPTU0MDtjZj13ZWJw/https://media.zenfs.com/zh-tw/game_nownews_com_902/4249e135bfeb536372a5865267714b09" class="wcard-img-top"  alt="..." style="width: 288px align-items: center">
                    <div class="card-body">
                        <p class="fw-bold">Become a volunteer!</p>
                        <p class="card-text">You time and talent can make a real different in people's life.</p>
                    </div>
                  </div>
            </div>
        </div>

        
            <button type="submit" class="btn btn-primary">Register</button>



    </form>







</template>