<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>
        <v-card>
            <v-card-title>
                <v-text-field
                    v-model="search"
                    append-icon="mdi-magnify"
                    label="Search"
                    single-line
                    hide-details
                ></v-text-field>
            <v-spacer></v-spacer>
            <v-btn color = "purple" dark @click="dialogHistory = true">
                Finished Todo
            </v-btn>
            <v-spacer></v-spacer>
            <v-btn color="success" dark @click="dialog = true">
                Tambah
            </v-btn>
            </v-card-title>
            <v-data-table :headers="headers" :items="todos" :search="search">
                <template v-slot:[`item.priority`]="{ item }">
                    <td>
                        <v-chip small label outlined :color="setColor(item.priority)">{{ item.priority }}</v-chip>
                    </td>
                </template>
                <template v-slot:[`item.actions`]="{ item }">
                    <v-icon small class="mr-2" @click="editItem(item)" icon color = "blue">
                        mdi-pencil
                    </v-icon>
                    <v-icon small class="mr-2" @click="deleteItem(item)" icon color ="red">
                        mdi-delete
                    </v-icon>
                </template>
                <template v-slot:[`item.checkbox`]="{ item }">
                    <v-checkbox multiple :key="item" @click.capture.stop="select(item)"></v-checkbox>
                </template>
            </v-data-table>
        </v-card>
        <v-dialog v-model="dialog" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Form Todo</span>
                </v-card-title>
                <v-card-text>
                    <v-container>
                        <v-text-field
                        v-model="formTodo.task"
                        label="Task"
                        required
                        ></v-text-field>
                        <v-select
                        v-model="formTodo.priority"
                        :items="['Penting', 'Biasa', 'Tidak penting']"
                        label="Priority"
                        required
                        ></v-select>
                        <v-textarea
                        v-model="formTodo.note"
                        label="Note"
                        required
                        ></v-textarea>
                    </v-container>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">
                        Cancel
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="save">
                        Save
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
        <v-dialog v-model = "dialogEdit" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Form Todo</span>
                </v-card-title>
                <v-card-text>
                    <v-container>
                        <v-text-field
                        v-model="formTodo.task"
                        label="Task"
                        required
                        ></v-text-field>
                        <v-select
                        v-model="formTodo.priority"
                        :items="['Penting', 'Biasa', 'Tidak penting']"
                        label="Priority"
                        required
                        ></v-select>
                        <v-textarea
                        v-model="formTodo.note"
                        label="Note"
                        required
                        ></v-textarea>
                    </v-container>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">
                        Cancel
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="edit">
                        Update
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
        <v-dialog v-model = "dialogDelete" persistent max-width="350px">
            <v-card>
                <v-card-title>
                    <p>Yakin Ingin Menghapusnya ??</p>
                </v-card-title>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="green" text @click="cancel"> Tidak </v-btn>
                    <v-btn color="red" text @click="deleteTodo"> Ya </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
        <v-dialog v-model="dialogHistory" persistent max-width="600px">
            <v-card>
                <v-data-table :headers="headers" :items="finishedTodo" :search="search"></v-data-table>
                <v-spacer></v-spacer>
                <v-card-actions>
                    <v-btn color="green" text @click="cancel">
                        Cancel
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
        <br>
        <v-card v-if="checked.length" persistent max-width="300px">
            <v-card-title>
                <p>Delete Multiple</p>
            </v-card-title>
            <v-list-item
                v-for="(item, i) in checked"
                :key="i">
                <v-list-item-content>
                    <v-list-item-title>•  {{item.task}}</v-list-item-title>
                </v-list-item-content>
            </v-list-item>
            <v-card-actions>
                <v-btn color="red" text @click="deleteChecked">
                    Delete All
                </v-btn>
            </v-card-actions>
        </v-card>
    </v-main>
</template>
<script>
export default {
    name: "List",
    data() {
        return {
            search: null,
            dialog: false,
            dialogEdit: false,
            dialogDelete: false,
            dialogHistory: false,
            finishedTodo: [],
            indexEdit: null,
            indexDelete: null,
            checked:[],
            headers: [
                {
                    text: "Task",
                    align: "start",
                    sortable: true,
                    value: "task",},
                { text: "Priority", value: "priority" },
                { text: "Note", value: "note" },
                { text: "Actions", value: "actions" },
                { text: "", value: "checkbox"},
            ],
            todos: [
                {
                    task: "bernafas",
                    priority: "Penting",
                    note: "huffttt",
                },
                {
                    task: "nongkrong",
                    priority: "Tidak penting",
                    note: "bersama tman2",
                },
                {
                    task: "masak",
                    priority: "Biasa",
                    note: "masak air 500ml",
                },
            ],
            formTodo: {
                task: null,
                priority: null,
                note: null,
            },
        };
    },
    methods: {
        save() {
            this.todos.push(this.formTodo);
            this.resetForm();
            this.dialog = false;
        },
        cancel() {
            this.resetForm();
            this.dialog = false;
            this.dialogEdit = false;
            this.dialogDelete = false;
            this.dialogHistory = false;
        },
        resetForm() {
            this.formTodo = {
                task: null,
                priority: null,
                note: null,
            };
        },
        editItem(item){
            this.dialogEdit = true;
            this.indexEdit = this.todos.indexOf(item);
            this.formTodo = {
                task: item.task,
                priority: item.priority,
                note: item.note,
            };
        },
        edit(){
            this.todos[this.indexEdit].task = this.formTodo.task;
            this.todos[this.indexEdit].priority = this.formTodo.priority;
            this.todos[this.indexEdit].note = this.formTodo.note
            this.dialogEdit = false;
        },
        deleteItem(item){
            this.dialogDelete = true;
            this.formTodo = item;

        },
        deleteTodo(){
            this.dialogDelete = false;
            this.finishedTodo.push(this.formTodo);
            this.indexDelete = this.todos.indexOf(this.formTodo);
            this.todos.splice(this.indexDelete, 1);
        },
        setColor(priority){
            if(priority == "Penting"){
                return 'red';
            } else if(priority == 'Biasa'){
                return 'blue';
            }else{
                return 'green';
            }
        },
        select(item){
            if(this.checked.includes(item)) {
                this.checked.splice(this.checked.indexOf(item), 1);
            } else {
                this.checked.push(item);
            }
        },
        deleteChecked(){
            for( var i=0 ; i<this.checked.length ; i++){
                const index = this.todos.indexOf(this.checked[i]);
                this.finishedTodo.push(this.checked[i]);
                this.todos.splice(index, 1);
            }
            this.checked=[];
            select(this.todos);
        }
    },
};
</script>