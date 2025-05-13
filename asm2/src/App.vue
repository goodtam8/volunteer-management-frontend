<script setup>
//do the general feature of the system
import { RouterLink, RouterView, useRouter, useRoute } from 'vue-router'
import { onMounted, ref } from 'vue'
import { jwtDecode } from "jwt-decode";



const router = useRouter();
const route = useRoute()

const search = ref("");
const role = ref("");

const token = ref(localStorage.getItem('token'))
// decode jwt token
const decoded = ref("");


const searchevent = async function () {
    // var url='/api/event';
    // var method='GET';

    // const response = await fetch(url, {
    //         method: method,
    //         headers: {
    //             'Content-Type': 'application/json'
    //         },
    //         body: JSON.stringify(search.value)
    //     });
    //     // convert the response to json
    //     const json = await response.json();
    //     // log the json
    //     console.log(json);
    //     // alert the user
    //     alert(JSON.stringify(json));
    router.push('/search/' + search.value);

}
const logout = function () {
    localStorage.removeItem('token');
    location.assign('/');
}
onMounted(() => {
    if (token.value) {
        decoded.value = jwtDecode(token.value);
        console.log(decoded);
        role.value = decoded.value.role;
    }
})
</script>

<template>
    <nav class="navbar navbar-expand-lg bg-body-tertiary">
        <div class="container-fluid">
            <a class="navbar-brand" href="#">Navbar</a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavAltMarkup"
                aria-controls="navbarNavAltMarkup" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNavAltMarkup">

                <div class="navbar-nav me-auto mb-2 mb-lg-0">
                    <a :class="{ 'nav-link': true, 'active': route.name === 'home' }" aria-current="page"
                        href="http://localhost:5173/">Home</a>
                    <a :class="{ 'nav-link': true, 'active': route.name === 'event' }"
                        href="http://localhost:5173/event">Events</a>
                    <a :class="{ 'nav-link': true, 'active': route.name === 'volunteer-create' }"
                        href="http://localhost:5173/volunteer/create" v-if="!token">Become volunteer</a>
                    <a :class="{ 'nav-link': true, 'active': route.name === 'list' }"
                        href="http://localhost:5173/volunteers" v-if="role == 'admin'">Volunteer</a>
                    <a :class="{ 'nav-link': true, 'active': route.name === 'list' }"
                        href="http://localhost:5173/account" v-if="role == 'volunteer'">My Events</a>


                    <input class="form-control" v-model="search" placeholder="Search..." />

                    <button type="button" class="btn btn-outline-success" v-on:click="searchevent">Search</button>
                    <button type="button" class="btn btn-outline-primary" @click="logout" v-if="token">Logout</button>
                    <a type="button" class="btn btn-primary" href="/login" v-if="!token">Login</a>


                </div>


            </div>

        </div>


    </nav>

    <RouterView />
</template>

