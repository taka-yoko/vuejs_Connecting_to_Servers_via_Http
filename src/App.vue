<template>
    <div class="container">
        <div class="row">
            <div class="col-xs-12 col-sm-8 col-sm-offset-2 col-md-6 col-md-offset-3">
                <h1>Http</h1>
                <div class='form-group'>
                    <label>Username</label>
                    <input type='text' class='form-control' v-model="user.name">
                </div>
                <div class='form-group'>
                    <label>Mail</label>
                    <input type='text' class='form-control' v-model="user.email">
                </div>
                <button class='btn btn-primary' @click="submit">Submit!</button>
                <hr>
                <button class="btn btn-primary" @click="fetchData">Get Data</button>
                <ul class="list-group mt-1">
                    <li class="list-group-item" v-for="user in users">{{ user.name }} - {{ user.email }}</li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                user: {
                    name: "",
                    email: ""
                },
                users: [],
                resource: {}
            };
        },
        methods: {
            submit() {
                // this.$http.post('', this.user)
                //     .then(response => {
                //         console.log(response);
                //     }, error => {
                //         console.log(error);
                // });
                this.resource.save({}, this.user);
            },
            fetchData() {
                this.$http.get('data.json')
                    .then(response => {
                        return response.json();
                    })
                    .then(data => {
                        const resultArray = [];
                        for (let key in data) {
                            resultArray.push(data[key]);
                        }
                        this.users = resultArray;
                    });
            }
        },
        created() {
            this.resource = this.$resource('data.json');
        }
    }
</script>

<style>
</style>
