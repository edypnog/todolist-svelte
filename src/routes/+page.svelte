<script>

    import { onMount } from "svelte";

    /**
     * @type {any[]}
     */
    let todos = [];
    let newTodo = '';

    onMount(() => {
        const savedTodos = localStorage.getItem('todos');
        if (savedTodos) {
            todos = JSON.parse(savedTodos);
        }
    })

    function addTodo() {
        if(newTodo.trim() !== '') {
            todos = [...todos, { id: Date.now(), text: newTodo, completed: false}]
            newTodo = '';
            saveTodos();
        }
    }

    /**
     * @param {any} id
     */
    function removeTodo(id) {
        todos = todos.filter((/** @type {{ id: any; }} */ todo) => todo.id!== id);
        saveTodos();
    }

    /**
     * @param {any} id
     */
    function toggleCompleted(id) {
        todos = todos.map((/** @type {{ id: any; completed: any; }} */ todo) => todo.id === id ? {...todo, completed: !todo.completed} : todo);
        saveTodos();
    }

    function saveTodos() {
        localStorage.setItem("todos", JSON.stringify(todos));
    }

</script>

<main>
    <h1>To-do List</h1>
    <div class="input-container">
        <input
            type="text"
            placeholder="Add a new todo..."
            bind:value={newTodo}
            on:keydown={(e) => e.key === 'Enter' && addTodo()}
        />
        <button on:click={addTodo}>Add</button>
    </div>

    <ul>
        {#each todos as todo }
            <li>
                <input type="checkbox" bind:checked={todo.completed} on:change={() => toggleCompleted(todo.id)} />
                <span class:completed={todo.completed}>{todo.text}</span>
                <button
                    class:done={todo.completed}
                    on:click={() => toggleCompleted(todo.id)}
                >Done</button>
                <button on:click={() => removeTodo(todo.id)}>Remove</button>
            </li>
        {/each}
    </ul>

</main>

<style>
    main {
        text-align: center;
        padding: 1em;
        max-width: 240px;
        margin: 0 auto;
        font-family: sans-serif;
    }

    .input-container {
        display: flex;
        align-items: center;
        margin-bottom: 1em;
    }

    input[type="text"] {
        flex: 1;
        padding: 0.5em;
        font-size: 1em;
        border: 1px solid #ccc;
        border-radius: 4px;
    }

/* Hide the default checkbox */
    input[type="checkbox"] {
        position: absolute;
        opacity: 0;
        pointer-events: none;
    }

    button {
        margin-left: 0.5em;
        font-size: 1em;
        padding: 0.5em 1em;
        border: none;
        background-color: #007BFF;
        color: white;
        border-radius: 4px;
        cursor: pointer;
    }

    ul {
        list-style: none;
        padding: 0;
        margin: 0;
    }

    li {
        display: flex;
        align-items: center;
        padding: 0.5em 0;
    }

    input[type="checkbox"] {
        margin-right: 0.5em;
    }

    .completed {
        text-decoration: line-through;
        color: #777;
    }

    .done {
    background-color: #007BFF; /* Set your desired color for the completed state */
    color: white;
    opacity: 0.6;
  }
</style>