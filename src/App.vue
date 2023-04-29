
<template>

  <div class="container mt-5" id="app">
    <!-- lopading -->
    <MyLoading v-if="Loading"/>

    <!-- header -->
    <div class="container-fluid mt-3 mb-5 p-0">
      <div class="container p-0">
        <h1 class="mt-2 mt-sm-0 text-center mb-5">COUNTRIES</h1>
      </div>
      <div class="container p-0 mt-2">
        <div class="row">
          <div class="col-xl-8 col-md-8 col-sm-8 col-6 d-flex col- justify-content-end">
            <input type="text" class="form-control" v-model="search_name" placeholder="Search country name">
          </div>
          <div class="col-xl-4 col-md-4 col-sm-4 d-flex col-6 justify-content-end ">
            <button @click="toggleSortOrder" class="btn btn-dark">{{ sortOrderButtonText }}</button>
          </div>
        </div>
      </div>
    </div>
    <!-- country card -->
    <div class="container-fluid p-0 mt-5">
      <div class="row">
        <div class="col-xl-3 col-md-6 col-sm-12 mb-4" v-for="country in sortedItems">
          <div class="container card border-0 pb-3">
            <img :src=country.flags.png alt="">
            <h5 class="mt-3" data-bs-toggle="modal" data-bs-target="#exampleModal"><span @click="DetailMore(country)" class="name">{{ country.name.official }}</span></h5>
            <p class="m-0"><b>cca2</b> : {{ country.cca2 }}</p>
            <p class="m-0"><b>cca3</b> : {{ country.cca3 }}</p>
            <p class="m-0 altSpellings"><b>altSpellings : </b>{{ country.altSpellings }}</p>
            <p class="m-0 idd"><b>idd : </b>{{ country.idd }}</p>
          </div>
        </div>
      </div>

      <!-- modal -->
      <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title" id="exampleModalLabel">Country Details</h5>
              <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <div class="modal-body">
              <img :src="img_Detail" alt="">
              <p><b>Country Name : </b>{{ name }}</p>
              <!-- <p><b>cca2 : </b>{{ nativeName }}</p> -->
              <p><b>cca2 : </b>{{ countryDetails.cca2 }}</p>
              <p><b>cca3 : </b>{{ countryDetails.cca3 }}</p>
              <p class="altSpellings"><b>altSpellings  : </b>{{ countryDetails.altSpellings  }}</p>
              <p class="idd"><b>idd : </b>{{ countryDetails.idd   }}</p>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-danger" data-bs-dismiss="modal">Close</button>
            </div>
          </div>
        </div>
      </div>

    </div>
  </div>

</template>

<script>
import axios from 'axios'
import VueAxios from 'vue-axios'
import MyLoading from './MyLoading.vue'

export default {
    components:{
            MyLoading
        },
        data(){
            return{
              countries:[],
              Loading: false,
              search_name: '',
              sortOrder: "asc",
              perPage: 4,
              currentPage: 1,
              rows: 200,
              countryDetails:[],
              img_Detail: false,
              name:false,
              nativeName:false,
            }
        },
        created(){
            this.getCountry()
        },
        computed:{
          filtere(){
            return this.countries.filter(country => {
              return country.name.official.toLowerCase().includes(this.search_name.toLowerCase())
            })
          },
          sortedItems() {
            let sortedArray = [...this.filtere];
            sortedArray.sort((a, b) => {
              let x = 0;
              if (a.name.official < b.name.official) {
                x = -1;
              } else if (a.name.official > b.name.official) {
                x = 1;
              }
              return x;
            });
            if (this.sortOrder === "desc") {
              sortedArray.reverse();
            }
            return sortedArray;
          },
          sortOrderButtonText() {
            return this.sortOrder === "asc" ? "Sort by Country Name Ascending ↑" : "Sort by Country Name Descending ↓";
          },
          rows() {
            return this.filtere.length
          }

        },

        methods: {
          async getCountry(){
            try{
              this.Loading = true;
              const response = await axios.get("https://restcountries.com/v3.1/all");
              this.countries = response.data;
              this.Loading = false;
            }
            catch(error){
              console.log(error);
            }
          },
          toggleSortOrder() {
            this.sortOrder = this.sortOrder == "asc" ? "desc" : "asc";
          },
          DetailMore(country) {
            this.countryDetails = country
            this.img_Detail = this.countryDetails.flags.png
            this.name = this.countryDetails.name.official
            this.nativeName = this.countryDetails.name.nativeName.zho.official

          }
        },
    }
</script>

<style scoped>
  .card{
    box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;
    height: 370px;
    overflow: hidden;
    background-color: #f3f3f3;
  }
  .card img{
    width: 100%;
    height: 180px;
    object-fit: cover;
    margin-top: 10px;
    border-radius: 5px;
  }
  .card h5{
    cursor: pointer;
  }
  .modal-body img{
    width: 100%;
    height: 240px;
    object-fit: cover;
    border-radius: 5px;
  }
  .idd , .altSpellings {
    overflow: hidden;
    text-overflow: ellipsis; 
    -webkit-line-clamp: 1;
    display: -webkit-box;
    -webkit-box-orient: vertical;
    white-space: normal;
  }
  .name{
    padding: 2px 10px ;
    background-color: #d4d4d4;
    border-radius: 8px;
  }
</style>
