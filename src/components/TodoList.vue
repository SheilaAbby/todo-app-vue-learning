<style scoped>
.todo-input {
	width:250px;
	padding: 10px 18px;
	font-size: 18px;
	margin-bottom: 16px;

	&:focus {
		outline: 0;
	}
}

.todo-item {
	margin-bottom: 12px;
	display: flex;
	align-items: center;
	justify-content: space-between;
	animation-duration: 0.3s;
}

.remove-item {
	cursor: pointer;
	margin-left: 14px;
	&:hover {
		color:black;
	}
}

.todo-item-left {
	display: flex;
	align-items: center;
}

.todo-item-label {
	padding: 10px;
	border: 1px solid white;
	margin-left: 12px;
}

.todo-item-edit {
	font-size: 24px;
	color: pink;
	margin-left: 12px;
	width: 200px;
	padding: 10px;
	border: 1px solid black;
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
}
.completed {
	text-decoration: line-through;
	color: green;
}
.extra-container {
	display: flex;
	align-items: center;
	justify-content: space-between;
	font-size: 16px;
	border-top: 1px solid lightgrey;
	padding-top: 14px;
	margin-top: 14px;
	margin-bottom: 14px
}
button {
	font-size: 14px;
	background-color: white;
	appearance: none;

	&:hover {
		background: lightgreen;
}
	&:focus {
		outline: none;
}
}

.active {
	background: lightgreen;
}

.fade-enter-active, .fade-leave-active {
	transition: opacity .2s;
}

.fade-enter, .fade-leave-to {
	opacity:0;
}
</style>

<template>
	<div class="todo-list">
		<b-container>
		<h1>Todo List</h1>
				<input type="text" class="todo-input" v-model="newTodo" placeholder="What needs to be done" @keyup.enter="addTodo">

		<transition-group name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
		<div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
			<div class="todo-item-left">
				<input type="checkbox" v-model="todo.completed">
				<div v-if="!todo.editing"  @dblclick="editTodo(todo)"class="todo-item-label" :class="{completed : todo.completed}" >{{todo.title}}
				</div>

				<input v-else type="text" v-model="todo.title" class="todo-item-edit" @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)" v-focus >
			</div>

				<div class="remove-item" @click="removeTodo(index)">
					&times;
				</div>
			</div>
		</transition-group>

			<div class="extra-container">
					<div>
						<label>
							<input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos">Check All
						</label>
					</div>
					<div>{{ remaining }} items left</div>
				</div>

			<div class="extra-container">
				<div>
					<button class="{ active: filter == 'all'}" @click="filter = 'all'">All</button>
					<button :class="{ active: filter == 'active'}" @click="filter = 'active'">Active</button>
					<button :class="{ active: filter == 'completed'}" @click="filter = 'completed'">Completed</button>
				</div>

					<div>
						<transition name="fade">
						<button v-if="showClearCompletedButton"
								@click="clearCompleted">
						Clear Completed
						</button>
					</transition>
					</div>
				</div>
				</b-container>
			</div>
</template>

<script>
	export default {
		name:'todo-list',

		data() {
			return {
			todo_id: 3,
			newTodo: '',
			beforeEditCache: '',
			filter: 'all',
			todos: [
			{
				'id':1,
				'title':'Finish Coding',
				'completed':false,
				'editing': false

			},
			{
				'id':2,
				'title':'Code vue.js',
				'completed':false,
				'editing': false
			},
			]
		}
		},
		computed: {
			remaining() {
				return this.todos.filter(todo => !todo.completed).length;
			},

			anyRemaining() {
				return this.remaining != 0
			},

			todosFiltered() {
			if(this.filter == 'all'){
				return this.todos
			}else if (this.filter == 'active'){
				return this.todos.filter(todo => !todo.completed)
			}else if(this.filter == 'completed'){
				return this.todos.filter(todo => todo.completed)
			}
			return this.todos
			},

			showClearCompletedButton() {
				//show this button when atleast 1 todo is completed
				return this.todos.filter(todo => todo.completed).length > 0

			}
		},
		
		directives: {
  			focus: {
			    inserted: function (el) {
			      el.focus()
			    }
			  }
			},
		methods: {
			
			addTodo: function(){
			if(this.newTodo.trim().length == 0){  //user can not submit an empty value
				return alert('Please enter something');
			}
				this.todos.push({
					id: this.todo_id,
					title: this.newTodo,
					completed: false
				})

				this.newTodo = ''
				this.todo_id++
			},

			editTodo: function(todo){
				this.beforeEditCache = todo.title
				todo.editing = true
			},
			cancelEdit: function(todo){
				todo.title =  this.beforeEditCache //this will grab the title before we edit it.
				todo.editing = false
			},
			removeTodo: function(index){
				this.todos.splice(index, 1)
			},

			doneEdit: function(todo){
				if(this.newTodo.trim() == ''){  //user can not submit an empty value
				todo.title = this.beforeEditCache
			}
				todo.editing = false
			},
			checkAllTodos() {
				this.todos.forEach((todo) => todo.completed = event.target.checked)
			},

			clearCompleted(){
				this.todos = this.todos.filter(todo => !todo.completed)
			}

		}
	}

	
</script>