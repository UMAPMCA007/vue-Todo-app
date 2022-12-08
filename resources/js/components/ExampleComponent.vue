<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-12">
                <div class="card">
                    <div class="card-header text-center">TODO LIST</div>

                    <div class="card-body row ">
                      <div class="col-md-10">
                        <input class=" form-control"  placeholder="Enter your todo" v-model='todo_input' />
                      </div>
                      <div class="col-md-2">
                        <button v-if="!edit_todo_id" type="submit" class="btn btn-success" @click="saveTodo()">Submit</button>
                        <button  v-else type="submit" class="btn btn-success" @click="updateTodo()">Update</button>
                        <button  class="btn btn-success" @click="resetTodo()">Reset</button>
                      </div>
                      <div class="col-md-12 mt-5">
                        <table class="table table-striped">
                        <thead>
                            <tr >
                                <th scope="col">#</th>
                                <th scope="col">Todo</th>
                                <th scope="col">Action</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(todo,index) in todos" :key="index">
                                <th scope="row">{{++index}}</th>
                                <th>{{todo.name}}</th>
                                <td> 
                                    <button class="btn btn-primary" @click="editTodo(--index)">Edit</button>
                                    <button class="btn btn-danger" @click="deleteTodo(--index)">Delete</button>
                                </td>
                            </tr>
                        </tbody>
                        </table>
                      </div>    
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return{
                todos:[],
                api:'api/todos',
                todo_input:"",
                edit_todo_id:"",
                edit_index:""
            }
        },
        mounted(){
            this.axios.get(this.api).then(res=>{
            this.todos=res.data;    

            });
        },
        methods:{
            saveTodo(){
                if(this.todo_input.length >0){
                    let data = {'name':this.todo_input};
                    this.axios.post(this.api,data).then(res=>{
                    this.todos.push(res.data); 
                    this.todo_input='';   

                    });  
                }
            },
            deleteTodo(index){
                console.log(this.todos[index]._id);
                if(this.todos[index]._id){
                    this.axios.delete(this.api+'/'+this.todos[index]._id).then(res=>{
                    this.todos=res.data;
                    });
                }
            },
            editTodo(index){
                this.todo_input=this.todos[index].name;
                this.edit_todo_id=this.todos[index]._id;
                this.edit_index=index;
            },
            updateTodo(index){
                let data={'name':this.todo_input};
                this.axios.put(this.api+'/'+this.todos[this.edit_index]._id,data).then(
                    res=>{
                    this.todos[this.edit_index].name=res.data.name; 
                    this.todo_input=''; 
                });
                this.resetTodo(); 
            },
            resetTodo(){
                this.todo_input="";
                this.todo_index="";
                this.edit_todo_id="";

            }
        }
    }
</script>
