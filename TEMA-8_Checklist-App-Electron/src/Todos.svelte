<script>

    import TodoItems from './TodoItems.svelte';

    // optional import focus-visible polyfill only once
    import 'focus-visible';

    // import any components
    import { Button, Checkbox, Datefield} from 'svelte-mui';
    

    let checked = true;
    let newTodoTitle = '';
    let currentFilter = 'all';
    let nextId = 3;

    let show = false;
    let count = 1;

    let toDoItemDate;
    let now = (("0" + (new Date().getDate() + 1)).slice(-2))+'-'+(("0" + (new Date().getMonth() + 1)).slice(-2))+'-'+new Date().getFullYear();

    // Todo Arrays
    let todos = [
        {
            id: 1,
            title: 'My first to-do',
            date: now,
            completed: false,
        },
        {
            id: 2,
            title: 'My second to-do',
            date: now,
            completed: false,
        }
    ];

    function addTodo() {
        if (newTodoTitle === ""){
            alert('Please enter atleast your To-Do');
        }
        else if (newTodoTitle.length > 30){alert('To-Do length cannot exceed 30 characters');}
        else {
            todos = [...todos, {
                id: nextId,
                completed: false,
                title: newTodoTitle,
                date: toDoItemDate,
            }];
            nextId = nextId + 1;
            newTodoTitle = '';
        }
         toDoItemDate = '';

    }

    $: todosRemaining = filteredTodos.filter(todo => !todo.completed).length;
    $: filteredTodos = currentFilter === 'all' ? todos : currentFilter === 'completed'
        ? todos.filter(todo => todo.completed)
        : todos.filter(todo => !todo.completed)
    function checkAllTodos(event) {
        todos.forEach(todo => todo.completed = event.target.checked);
        todos = todos;
    }
    function updateFilter(newFilter) {
        currentFilter = newFilter;
    }
    function clearCompleted() {
        todos = todos.filter(todo => !todo.completed);
    }
    function handleDeleteTodo(event) {
        todos = todos.filter(todo => todo.id !== event.detail.id);
    }
    function handleToggleComplete(event) {
        const todoIndex = todos.findIndex(todo => todo.id === event.detail.id);
        const updatedTodo = { ...todos[todoIndex], completed: !todos[todoIndex].completed};
        todos = [
            ...todos.slice(0, todoIndex),
            updatedTodo,
            ...todos.slice(todoIndex+1)
        ];
    }

//Function for Button 'ENTER' 

function submit(event) {
  if (event.key === 'Enter') {
    addTodo();
  }
}

//$: console.log(toDoItemDate)


</script>

<main>
    <div class="container">
        <h1>Checklist</h1>
        <input type="text" class="todo-input" placeholder="click a to-do, select target date, and hit enter..." bind:value={newTodoTitle} on:keydown={submit}>

        <div class="wrapper">
            <div class="date">
                <Datefield format='DD-MM-YYYY' 
                    icon=false
                    bind:value={toDoItemDate}
                ></Datefield>
            </div> 
        
        <button class="enterButton" on:click="{addTodo}">Enter</button>
    
        </div>
        {#each filteredTodos as todo}
            <div class="todo-item">
                <TodoItems {...todo} on:deleteTodo={handleDeleteTodo} on:toggleComplete={handleToggleComplete} />
            </div>
        {/each}
        <div class="inner-container">
            <div><label><input class="inner-container-input" type="checkbox" on:change={checkAllTodos}>Check All</label></div>
            <div class="leftItems">{todosRemaining} Items Left</div>
        </div>
        <div class="inner-container">
            <div>
                <button on:click={() => updateFilter('all')} class:active="{currentFilter === 'all'}"><strong>All</strong></button>
                <button on:click={() => updateFilter('active')} class:active="{currentFilter === 'active'}">Active</button>
                <button on:click={() => updateFilter('completed')} class:completed="{currentFilter === 'completed'}">Completed</button>
            </div>
            <div>
                <button on:click={clearCompleted}>Clear Completed</button>
            </div>
        </div>
    </div>
 
</main>

<style>

	:global(:root) {
        --primary: #665A70;
	}
    :global(body){
        background-color: lightseagreen;
        background-image: url("https://image.freepik.com/free-photo/multicolored-bright-soft-bubbles-glowing-drops_23-2148205139.jpg");
        background-repeat: no-repeat;
        background-size: cover;
        background-attachment: fixed;
        padding: 1rem;
        box-sizing: border-box;

    }
    :global(*){
        box-sizing: border-box;
    }

    /* main{
        background: rgb(250, 250, 250, 0.9);
        border-radius: 5px;
        display: grid;
        place-items: center;
        height: 100%;
    }*/

    .container{
        max-width: 100vw;
        margin: 0 auto;
        padding:2rem;
        background: rgb(250, 250, 250, 0.9);
        border-radius: 5px;
        height: 100%;
        display: flex;
        flex-direction: column;
        min-height: 96vh;
        height: 100%;
    }


    h1{
        font-size: 5rem;
        color: #AD47FF;
        text-align: center;
        margin: 0 50px 0 50px;
        padding: 0 50px 20px 50px;
        font-family: 'Quicksand', sans-serif;
    }

    .date{
        display: inline-block;
        width:200px;
        height:50px;
        margin-right: 50px;
    }

    .enterButton{
        display: inline-block;
        width:100px;
        height:40px;
        background-color: #AD47FF;
        font: 10px;
        letter-spacing: 1.25px;
        color: white;
        text-transform: uppercase;
        border-radius: 5px;
        font-family: 'Quicksand', sans-serif;
    }

    .enterButton:hover{
        background-color: #5e298a;
    }

    .todo-input {
        width: 100%;
        padding: 10px, 20px 0px 20px;
        font-size: 18px;
        margin-bottom: 0;
        font-family: 'Quicksand', sans-serif;
        border-radius: 5px;
        background: rgb(230, 225, 235);
        border: none;
    }

    .todo-item{
        font-family: 'Montserrat', sans-serif;
    }
    
    .leftItems{
        font-family: 'Montserrat', sans-serif;
    }
    .inner-container {
        display: flex;
        align-items: center;
        justify-content: space-between;
        font-size: 16px;
        border-top: 2px solid lightgray;
        padding-top: 15px;
        margin-bottom: 13px;
 
    }
    .inner-container-input {
        margin-right: 12px;
        font-family: 'Montserrat', sans-serif;
    }

    button {
        font-size: 18px;
        background-color: rgb(230, 225, 235);
        border-radius: 5px;
        appearance: none;
        border: none;
        font-family: 'Quicksand', sans-serif;
    }
    button:hover {
        background: lightseagreen;
    }
    button:focus, .enterButton:focus {
        outline: none;
    }
    .active, .completed {
        background: lightseagreen;
    }

    @media (max-width: 630px){
          h1{
            display: grid;
            justify-content: center;
        }
        .enterButton{
            margin: 10px;
        }
 
    }

</style>
