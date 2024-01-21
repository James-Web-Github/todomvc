

<template>
	<body>
		<section class="todoapp">
			<header class="header">
				<h1>todos</h1>
				<input @keyup.enter="addTodo" class="new-todo" placeholder="What needs to be done?" autofocus>
			</header>
			<!-- This section should be hidden by default and shown when there are todos -->
			<section class="main">
				<input  id="toggle-all" class="toggle-all" type="checkbox" @click="toggleAll">
				<label for="toggle-all">Mark all as complete</label>
				<ul class="todo-list">
					<!-- These are here just to show the structure of the list items -->
					<!-- List items should get the class `editing` when editing and `completed` when marked as completed -->

					<li v-for="todo in filterToDo"
          :key="todo.id"
          :class="{ completed: todo.completed }">
						<div class="view">
							<input class="toggle" type="checkbox" v-model="todo.completed">
							<label>{{ todo.title }}</label>
							<button class="destroy" @click="removeTodo(todo)"></button>
						</div>
						<input class="edit" value="Create a TodoMVC template">
					</li>
				</ul>
			</section>
      <!-- This footer should be hidden by default and shown when there are todos -->
			<footer class="footer">
				<!-- This should be `0 items left` by default -->
				<span class="todo-count"><strong>{{ remaining }}</strong> item left</span>
				<!-- Remove this if you don't implement routing -->
				<ul class="filters">
					<li>
						<a  :class="{ selected: visibility === 'all'}" href="#/all">All</a>
					</li>
					<li>
						<a :class="{ selected: visibility === 'active'}" href="#/active">Active</a>
					</li>
					<li>
						<a :class="{ selected: visibility === 'completed'}" href="#/completed">Completed</a>
					</li>
				</ul>
				<!-- Hidden if no completed items are left ↓ -->
				<button @click="clearCompleted" class="clear-completed">Clear completed</button>
			</footer>
		</section>
  </body>
</template>

<script>
export default {
  data () {
    return {
      todos: [
        {
          id: 12,
          title: "test content1",
          completed: true 
        },
        {
          id: 34,
          title: "test content2",
          completed: false 
        },
      ],
      visibility: 'all'
    }
  },
  
  methods: {
    //e.target 就代表了被点击的那个按钮元素
    // <input type="checkbox"> 被点击时，e.target 就是指向这个 checkbox 的引用，因此 e.target.checked 就可以用来获取这个 checkbox 当前的勾选状态。
    toggleAll (e) {
      this.todos.forEach(todo => todo.completed = e.target.checked)
    },
    onHashChange () {
		const hash = window.location.hash.replace(/#\/?/,'')
		if (['all','active','completed'].includes(hash)) {
		this.visibility = hash
		console.log(hash)
		} else {
		window.location.hash = ''
		this.visibility = 'all'
		}
    },
	addTodo (e) {
		const title = e.target.value.trim()//去除空格
		if (!title) {
			return
		}
		this.todos.push({
			id:Date.now(),
			title,
			completed: false
		})
		e.target.value = ''
	},
	removeTodo (todo) {
		this.todos.splice(this.todos.indexOf(todo),1)
	},
	clearCompleted () {
		this.todos = this.todos.filter(todo => !todo.completed)
	}
  },
  computed: {
	filterToDo () {
      switch (this.visibility) {
		case 'all':
			return this.todos;
		case 'active':
			return this.todos.filter(todo => !todo.completed);
		case 'completed':
			// 在todos裡面過濾 todo的completed屬性為true的
			// return this.todos.filter(todo => todo.completed === true)
			return this.todos.filter(todo => todo.completed)
		//原本是不用加的，但vue再生成html 遇到v-for的函式呼叫時，還沒有完成data的建置，所以會找不到函數導致錯誤，
		//在第一次會先走default，頁面加載好會再重新渲染一次
		default:
			return []
		}
	},
	remaining () {
		return this.todos.filter(todo => !todo.completed).length
	}
  },
  // 單頁應用（SPA）路由：在單頁應用中，window.location.hash 用於處理前端路由。這意味著當哈希改變時，
  // 可以根據新的哈希值來動態加載或顯示不同的內容，而無需重新加載頁面。
  mounted () {
    window.addEventListener('hashchange', this.onHashChange)
	this.onHashChange()
  }
}
</script>
<style>
/* @import  url('todomvc-common/base.css');*/
@import  url('../node_modules/todomvc-app-css/index.css');
/* @import "https://unpkg.com/todomvc-app-css@2.4.1/index.css" */
</style>
