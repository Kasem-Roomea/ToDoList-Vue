<script setup>
  import {ref,onMounted , computed , watch} from 'vue'

  const toDos = ref([]);
  const name = ref('');
  const input_content = ref('');
  const input_category = ref(null);


  const todos_asc = computed(() => toDos.value.sort((a,b) =>{
    return b.createdAt - a.createdAt
  }));


  const addTodo= ()=>{
    if(input_content.value.trim()==='' || input_category.value === null)
    {
      return
    }
    toDos.value.push({
    content: input_content.value,
    category: input_category.value,
    done:false,
    editable:false,
    createdAt: new Date().getTime()
    });

    input_category.value = null;
    input_content.value = '';
  };

  const removeTodo = (todo) => {
    toDos.value = toDos.value.filter((t) => t !== todo);
  };




  watch(name, (newVal) => {
    localStorage.setItem('name', newVal)
  });
  watch(toDos, (newVal) => {
    localStorage.setItem('toDos', JSON.stringify(newVal))
  },{deep:true});




  onMounted(()=>
  {
    name.value = localStorage.getItem('name') || '';
    toDos.value = JSON.parse(localStorage.getItem('toDos') || []);
  });

</script>

<template>

  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" id="name" placeholder="Name here" v-model="name">
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
                type="text"
                name="content"
                id="content"
                placeholder="e.g. make a video"
                v-model="input_content" />

        <h4>Pick a category</h4>
        <div class="options">

          <label>
            <input
                    type="radio"
                    name="category"
                    id="category1"
                    value="business"
                    v-model="input_category" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input
                    type="radio"
                    name="category"
                    id="category2"
                    value="personal"
                    v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>

        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list" id="todo-list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category === 'business'? 'business': 'personal'}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>

      </div>
    </section>
  </main>

</template>


