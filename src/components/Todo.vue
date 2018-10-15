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
                        <button type="button" v-on:click="addNewItem()" class="btn btn-add">Add</button>
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
                        <div>
                            <input class="itemComplete" type="checkbox" v-model="todo.isComplete" v-on:change="updateItem(todo)"> 
                            <label class="itemTitle strikethrough" v-on:click="editValues(todo)">{{ todo.title }}</label>
                            <i class="itemCreated">{{ formatDate(todo.created) }}</i>
                            <button class="btn-trash" v-on:click="removeItem(todo.id, index)">
                                <span class="glyphicon glyphicon-trash"></span>
                            </button>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
        <Item :todoItemProp="todoCurrentItem" v-on:sendToSave="saveItem"/>
    </div>
</template>

<script>
  import axios from 'axios';
  import Item from './Item';

    export default {
        name: "Todo",
        components :{
            Item
        },
        data () {
            return {
                todos: [],
                todoCurrentItem: Object,
                input: "",
                loading: false
            }
        },
        mounted () {
            this.loading = true;
            axios
            .get('http://todoapi.us-west-2.elasticbeanstalk.com/api/todo/actives')
            .then(response => {
                this.todos = response.data;
                this.loading = false;
            });
        },
        addNewItem() {
            this.todoCurrentItem = { "title": this.input }
            $('#itemModal').modal('show');
        },
        editValues(itemToEdit) {
            this.todoCurrentItem = itemToEdit;
            $('#itemModal').modal('show');
        },
        methods: {
            saveItem(itemToSave) {
                if(itemToSave.id == undefined)
                    this.addItem(itemToSave);
                else
                    this.updateItem(itemToSave);

                this.todoCurrentItem = {};
            },
            addItem(itemToAdd) {
                this.loading = true;
                axios
                .post('http://todoapi.us-west-2.elasticbeanstalk.com/api/todo', itemToAdd)
                .then(response => {
                    this.todos.push(response.data); 
                    this.input = "";
                    this.loading = false;
                });
            },
            removeItem(id, index) {
                this.loading = true;
                axios
                .put('http://todoapi.us-west-2.elasticbeanstalk.com/api/todo/remove/' + id)
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
        outline: 0;
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
        font-size:15px;
        float:left;        
    }
    .itemTitle:hover {
        cursor: pointer;
        text-decoration: underline;
    }
    .itemCreated {
        padding-left: 8px;
        line-height: 22px;
        font-size: 12px;
        float:left;
    }
    .btn-trash{
        border:0px solid transparent;
        outline: 0;
        font-size:18px;
        float: right;
    }
    .glyphicon-trash {
        color: #C7723E;
    }
    .loader {
        padding-top: 5%;
    }
    input[type=checkbox]:checked + label.strikethrough{
        text-decoration: line-through;
    }
</style>