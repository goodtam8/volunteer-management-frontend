<script setup>
import { jwtDecode } from "jwt-decode";
import { ref, onMounted, computed,nextTick, watch } from 'vue'
const token = ref(localStorage.getItem('token'))
const decoded = ref("");
const num=ref("");
const role = ref("");
const data = ref([]);
const total = ref(0);
const loading = ref(false);
const sortField = ref("vote_count");
const sortOrder = ref("desc");
const defaultSortOrder = ref("desc");
const page = ref(1);
const perPage = ref(20);

const loadAsyncData = () => {
    const params = [
        //"api_key=bb6f51bef07465653c3e553d6ab161a8",
        //"language=en-US",
        //"include_adult=false",
        //"include_video=false",
        //`sort_by=${sortField.value}.${sortOrder.value}`,
        `page=${page.value}`,
    ].join("&");
    loading.value = true;
    fetch(`/api/enroll?${params}`, {
            headers: {
                Authorization: `Bearer ${token.value}`
            }
        })//加返page ge 野 lab6 ge 野)
.then((response) => response.json())
.then((result) => {
    perPage.value = result.perPage;
    total.value = result.total;
    data.value = result.volunteer
    
    loading.value = false;
})};
         

/*
 * Handle page-change event
 */
const onPageChange = (p) => {
    page.value = p;
    loadAsyncData();
};

/*
 * Handle sort event
 */
const onSort = (field, order) => {
    sortField.value = field;
    sortOrder.value = order;
    loadAsyncData();
};

/*
 * Type style in relation to the value
 */
const type = (value) => {
    const number = parseFloat(value);
    if (number < 6) {
        return "is-danger";
    } else if (number >= 6 && number < 8) {
        return "is-warning";
    } else if (number >= 8) {
        return "is-success";
    }
};

onMounted(() => {
    if (token.value) {
        decoded.value = jwtDecode(token.value);
        console.log(decoded.value);
        role.value = decoded.value.role;
        num.value=decoded.value._id
    }
    loadAsyncData();
});


</script>

<template>
     <nav aria-label="breadcrumb">
        <ol class="breadcrumb">
            <li class="breadcrumb-item"><a href="http://localhost:5173/">Home</a></li>
            <li class="breadcrumb-item active" aria-current="page">Volunteers</li>
        </ol>
    </nav>
    <br>
    <a class="btn btn-primary" href="http://localhost:5173/volunteer/create" role="button">New</a>


<section>
    <o-table
        :data="data"
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
        
        <o-table-column
            v-slot="props"
            field="name"
            label="Volunteer name"
            sortable>


          {{ props.row.name }}
        </o-table-column>
        <o-table-column
            v-slot="props"
            field="email"
            label="Email"
            numeric
            sortable>
            
                {{ props.row.email }}
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

        </o-table-column>
    </o-table>
</section>


</template>