<template>
    <div class="centered">
        <div class="row">
            <div class="col-md-12">
                <div>   
                    <h2 id="mainTitle"><span class="glyphicon glyphicon-list"></span> To-Do List</h2>
                    <form>
                        <div class="form-group">
                            <input type="text" v-model="input" id="todoitem" placeholder="What needs to be done?" maxlength="30"/>
                        </div>
                        <button type="button" v-on:click="addItem()" class="btn btn-add">Add</button>
                    </form>
                </div>
            </div>
        </div>
        <div v-if="loading" class="loader">
            <img src="../assets/ajax-loader.gif" alt="loader">
        </div>
        <div class="row">
            <div class="col-md-12">
                <ul class="list-group">
                    <li v-for="(todo, index) in todos" :key="index" class="list-group-item">
                        <input class="itemComplete" type="checkbox" v-model="todo.isComplete" v-on:change="updateItem(todo)"> 
                        <span class="itemTitle">{{ todo.title }}
                            <i class="itemCreated">{{ formatDate(todo.created) }}</i>
                        </span>
                        <button class="btn-trash" v-on:click="deleteItem(todo.id, index)">
                            <span class="glyphicon glyphicon-trash"></span>
                        </button>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
  import axios from 'axios';

    export default {
        name: "Todo",
        data () {
            return {
                todos: [],
                input: "",
                loading: false
            }
        },
        mounted () {
            this.loading = true;
            axios
            .get('http://todoapi.us-west-2.elasticbeanstalk.com/api/todo')
            .then(response => {
                this.todos = response.data;
                this.loading = false;
            });
        },
        methods: {
            addItem() {
                var newItem = {"title": this.input};
                this.loading = true;
                axios
                .post('http://todoapi.us-west-2.elasticbeanstalk.com/api/todo', newItem)
                .then(response => {
                    this.todos.push(response.data); 
                    this.input = "";
                    this.loading = false;
                });
            },
            deleteItem(id, index) {
                this.loading = true;
                axios
                .delete('http://todoapi.us-west-2.elasticbeanstalk.com/api/todo/' + id)
                .then(response => {
                    this.todos.splice(index, 1);
                    this.loading = false;
                });
            },
            updateItem(todo) {
                this.loading = true;
                axios
                .put('http://todoapi.us-west-2.elasticbeanstalk.com/api/todo/' + todo.id, todo)
                .then(response => { 
                    this.loading = false 
                });
            }, formatDate(date) {
                var newDate = new Date(date);
                return newDate.toLocaleString(newDate, { month: "short" }) + ' ' +newDate.getDate();
            }
        }
    }
</script>

<style>
    body{
        min-width:600px;
        width: auto !important;
        width:600px;
    }
    span {
        color:#505050;
    }
    .centered {
        width: 60%;
        margin: 0 auto;
        overflow: hidden;
        align-items: center;
        justify-content: space-around;
        float: none;
    }
    .btn-add {
        width:40%;
        background-color: #505050;
        color:white;
        font-weight: bold;
    }
    .btn-add:hover {
        color:white;
    }
    .row {
        padding-top: 50px;
    }
    #todoitem {
        width: 80%;
        font-size:20px;
        border: 0;
        outline: 0;
        background: transparent;
        border-bottom: 1px solid #505050;
    }
    #todoitem:focus { 
        border-bottom: 3px solid #C7723E;
    }
    #mainTitle {
        font-size:36px;
        color: #C7723E;
        padding-bottom: 8%;
    }
    .list-group-item {
        padding: 10px;
        height: 40px;
    }
    .itemComplete {
        transform: scale(1.4);
        float:left;
    }
    .itemTitle {
        padding-left: 8px;
        font-size:16px;
        float:left;
    }
    .itemCreated {
        font-size: 12px;
    }
    .btn-trash{
        border:0px solid transparent;
        font-size:18px;
        float: right;
    }
    .glyphicon-trash {
        color: #C7723E;
    }
    .loader {
        padding-top: 5%;
    }
</style>