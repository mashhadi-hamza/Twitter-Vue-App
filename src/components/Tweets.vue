<template>
    <div class="container">
        <h3>Twitter API</h3>
        <div class="container">
            <div class="row mb-5">
                <div class="col-lg-8 mx-auto">
                    <h5 class="font-weight-light mb-4 font-italic text-white">Some Search Options For You :</h5>
                    <h6 class="font-weight-light mb-4 font-italic text-white">Type Search String OR User Handle OR Both</h6>
                    <h6 class="font-weight-light mb-4 font-italic text-white">Press Enter OR Click Search Icon</h6>
                    <div class="bg-white p-5 rounded shadow">
                        <div v-if="queryError" class="alert alert-danger">
                            <strong>Error!</strong> {{queryErrorText}}
                        </div>
                        <!-- search bars -->
                            <div class="p-1 bg-light rounded rounded-pill shadow-sm mb-4">
                                <div class="input-group">
                                    <input v-model="fetchQuery" @keyup.enter="fetchTweets()" type="search" placeholder="Enter String To Search " aria-describedby="button-addon1" class="form-control border-0 bg-light">
                                    <div class="input-group-append">
                                        <button v-on:click="fetchTweets()" id="button-addon1" class="btn btn-link text-primary"><i class="fa fa-search"></i></button>
                                    </div>
                                </div>
                            </div>
                            <div class="p-1 bg-light rounded rounded-pill shadow-sm mb-4">
                                <div class="input-group">
                                    <div class="input-group-prepend">
                                        <button v-on:click="fetchTweets()" id="button-addon2" class="btn btn-link text-warning"><i class="fa fa-search"></i></button>
                                    </div>
                                    <input v-model="fetchHandle" @keyup.enter="fetchTweets()" type="search" placeholder="Enter User handle To Search" aria-describedby="button-addon2" class="form-control border-0 bg-light">
                                </div>
                            </div>
                        <!-- End -->
                    </div>
                </div>
            </div>
        </div>


        <table v-if="tweets" class="table-bordered table-hover table-dark table-striped">
            <thead>
            <tr>
                <th scope="col">Id</th>
                <th scope="col">Text</th>
                <th scope="col">Created At</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="tweet in tweets" v-bind:key="tweet.id">
                <th scope="row">{{tweet.id}}</th>
                <td>{{tweet.text}}</td>
                <td>{{tweet.created_at}}</td>
            </tr>
            </tbody>
        </table>
        <h5 v-else class="font-weight-light mb-4 font-italic text-white">No results to show...</h5>
    </div>
</template>
<script>
    import axios from 'axios';
    import config from '../config/config';
    export default {
        name: 'Tweets',
        data() {
            return {
                tweets: null,
                fetchQuery:"",
                fetchHandle:"",
                queryError:false,
                queryErrorText:"",
            };
        },
        methods:{
            fetchTweets(){
                this.queryError = false;
                let queryURL = null;
                if(!this.fetchQuery && !this.fetchHandle){
                    this.queryError = true;
                    this.queryErrorText = "Please add some Query String OR User Handle before clicking Search icon.";
                }
                if(this.fetchQuery && this.fetchHandle){
                    queryURL =config.apiURL+'/twitter/searchByUser/'+this.fetchHandle+'/'+this.fetchQuery;
                }else if(this.fetchHandle){
                    queryURL = config.apiURL+'/twitter/searchByUser/'+this.fetchHandle;
                }else{
                    queryURL = config.apiURL+'/twitter/searchByUser/'+this.fetchQuery
                }
                axios
                    .get(queryURL)
                    .then(res => {
                        this.tweets= res.data;
                    }).catch(err =>{
                    console.log("error occurred: "+err);
                });
            }
        }
    }
</script>
<style>
    h3 {
        margin-bottom: 5%;
    }
</style>