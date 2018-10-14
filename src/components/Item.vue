<template>
  <div class="modal fade" id="itemModal" role="dialog">
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content">
        <div class="modal-header">
          <button type="button" class="close" data-dismiss="modal">&times;</button>
          <input class="title" v-model="todoItemProp.title" id="title"/>
        </div>
        <div class="modal-body">
          <textarea v-model="todoItemProp.description" id="description" placeholder="Description" maxlength="200"/>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-custom" data-dismiss="modal" v-on:click="sendToSave">Save</button>
          <button type="button" class="btn btn-default" data-dismiss="modal">Cancel</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
    export default {
        name: "Item",
        props: {
            todoItemProp: {}
        },
        data () {
            return {
            }
        },
        computed: {
            todoItemPropLocal: function(){
                return this.todoItemProp;
            }
        },
        methods: {
            sendToSave (event) {
                this.$emit('sendToSave', this.todoItemPropLocal);
            }
        }
    }
</script>

<style>
    .modal-body {
        height: 120px;
    }
    .title {
        width:100%;
        font-size: 18px;
        font-weight: bold;
        border: 0;
        outline: 0;
        background: transparent;
    }
    #description {
        width: 100%;
        height: 100%;
        resize: none;
        border: 0;
        outline: 0;
        font-size: 14px;
    }
    .btn-custom {
        background-color: #505050;
        color:white;
    }
    .modal-dialog-centered {
        padding-top: 20vh; 
    }
</style>