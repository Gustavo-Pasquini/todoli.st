<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

interface Todo {
  id: number
  text: string
  createdAt: number // timestamp em ms
  completedAt?: number // timestamp em ms
}

const todos = ref<Todo[]>([])
const newTodo = ref('')
let nextId = 1

const now = ref(Date.now())

onMounted(() => {
  const interval = setInterval(() => {
    now.value = Date.now()
  }, 1000)

  onUnmounted(() => {
    clearInterval(interval)
  })
})

function addTodo() {
  if (newTodo.value.trim()) {
    todos.value.push({
      id: nextId++,
      text: newTodo.value,
      createdAt: Date.now(),
    })
    newTodo.value = ''
  }
}

function completeTodo(todo: Todo) {
  if (!todo.completedAt) {
    todo.completedAt = Date.now()
  }
}

function getElapsed(todo: Todo) {
  const end = todo.completedAt ?? now.value
  let diff = Math.ceil((end - todo.createdAt) / 1000)
  
  if (diff < 0) 
    diff = 0

  const hrs = Math.floor(diff / 3600)
  const min = Math.floor((diff % 3600) / 60)
  const sec = diff % 60

  return `${hrs.toString().padStart(2, '0')}:${min.toString().padStart(2, '0')}:${sec.toString().padStart(2, '0')}`
}

</script>

<template>
  <div class="todo-bg">
    <div class="todo-header">
      <img src="/assets/logo.png" alt="logo" style="height: 64px; width: 64px;" />
      <h1>todoli.st</h1>
      <form @submit.prevent="addTodo" class="todo-form">
        <input v-model="newTodo" placeholder="nova tarefa..." autofocus />
        <button type="submit">adicionar</button>
      </form>
    </div>
    <div class="todo-container">
      <div v-if="todos.length === 0" class="empty">nenhuma tarefa ainda.</div>
      <div v-for="todo in todos" :key="todo.id" class="todo-card" :class="{ done: todo.completedAt }">
        <div class="todo-text">{{ todo.text }}</div>
        <div class="timer">
          <span v-if="!todo.completedAt">tempo: {{ getElapsed(todo) }}</span>
          <span v-else>concluída em: {{ getElapsed(todo) }}</span>
        </div>
        <button v-if="!todo.completedAt" @click="completeTodo(todo)">Concluir</button>
      </div>
    </div>
  </div>
  <footer class="footer">
    <div class="social-links">
      <a
        class="social-button linkedin"
        href="https://www.linkedin.com/in/gustavo-pasquini-6596082a9/"
        target="_blank"
      >
        <img src="/assets/linkedin.svg" alt="LinkedIn" />
      </a>
      <a
        class="social-button github"
        href="https://github.com/Gustavo-Pasquini"
        target="_blank"
      >
        <img src="/assets/github.svg" alt="GitHub" />
      </a>
    </div>

    <div class="copyright">
      © 2025 Gustavo Pasquini
    </div>
  </footer>

</template>

<style scoped>
.todo-bg {
  flex: 1; /* Permite que o todo-bg ocupe o espaço restante */
  display: flex;
  flex-direction: column;
  align-items: center; /* Centraliza horizontalmente o conteúdo do todo-bg */
  background: none;
  width: 100%; /* Garante que o todo-bg ocupe a largura total para centralizar o conteúdo */
  min-height: calc(100vh - 100px);
  margin-bottom: 200px;
}
.todo-header {
  width: 100%;
  max-width: 400px;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 48px;
  margin-bottom: 32px;
  border: solid;
  border-color: #fff;
  border-radius: 1rem;
  border-width: 1px;
  padding: 2rem;
}
h1 {
  text-align: center;
  margin-top: 0;
  margin-bottom: 2rem;
  font-size: 2.6rem;
  color: #fff;
  letter-spacing: 1px;
  font-family: 'Poiret One', cursive, sans-serif;
  font-weight: bold;
  text-shadow: 0 1px 0 #fff2, 0 2px 2px #0004;
}
.todo-form {
  display: flex;
  gap: 8px;
  justify-content: center;
  width: 100%;
  max-width: 400px;
}
.todo-form input {
  flex: 1;
  padding: 8px;
  border: 1px solid #444;
  border-radius: 6px;
  font-size: 1rem;
  background: #222;
  color: #fff;
  font-family: 'Poiret One', cursive, sans-serif;
  font-weight: bold;
  letter-spacing: 1px;
  caret-color: #dddddd2f;
}
.todo-form input:focus {
  outline: 1px solid #eee;
}

.todo-form input:focus::placeholder {
  color: #ddd;
}

.todo-form button {
  padding: 8px 14px;
  border: none;
  background: #fff;
  color: #222;
  border-radius: 6px;
  cursor: pointer;
  font-size: 1rem;
  font-weight: 500;
  transition: background 0.2s, color 0.2s;
  font-family: 'Poiret One', cursive, sans-serif;
  font-weight: bold;
  letter-spacing: 1px;
}
.todo-form button:hover {
  background: #4e4093;
  color: #fff;
}
.todo-container {
  width: 100%;
  max-width: 400px;
  margin: 0;
  padding: 24px;
  background: #4e409379;
  border-radius: 0.8rem;
  box-shadow: 0 2px 16px #0002;
  display: flex;
  flex-direction: column;
  align-items: stretch;
}
.empty {
  text-align: center;
  color: #eee;
  margin: 24px 0;
}
.todo-card {
  background: #f9f9f9;
  border-radius: 8px;
  padding: 14px 12px;
  margin-bottom: 12px;
  display: flex;
  flex-direction: column;
  gap: 6px;
  box-shadow: 0 1px 4px #0001;
  transition: background 0.2s;
}
.todo-card.done {
  background: #fff;
  text-decoration: line-through;
  color: #000;
}
.todo-text {
  font-size: 1.1rem;
  color: #000;
  font-family: 'Poiret One', cursive, sans-serif;
  font-weight: bold;
  letter-spacing: 1px;
}
.timer {
  font-size: 0.95rem;
  color: #000;
}
.todo-card button {
  align-self: flex-end;
  padding: 4px 10px;
  border: none;
  background: #4e4093;
  color: #fff;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.95rem;
}
.todo-card.done button {
  display: none;
}

/* Estilos globais */
body {
  margin: 0;
  background: #222;
  font-family: 'Poiret One', cursive, sans-serif;
  font-weight: bold;
  padding-bottom: 100px; /* Adiciona um padding na parte inferior */
  box-sizing: border-box;
}

footer.footer {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 100px; 
  background-color: #4e4093;
  color: #fff;
  font-family: 'Poiret One', cursive, sans-serif;
  font-weight: bold;
  letter-spacing: 1px;
  box-shadow: 0 -2px 8px #0002;
  border-radius: 0.8rem 0.8rem 0 0;
  text-align: center;
  padding: 12px 0;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.social-links {
  display: flex;
  justify-content: center;
  gap: 16px;
  margin-bottom: 8px;
}

.social-button {
  background-color: #fff;
  border-radius: 50%;
  width: 32px;
  height: 32px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  transition: transform 0.2s;
}

.social-button:hover {
  transform: scale(1.1);
}

.social-button.linkedin {
  background-color: #0082ca79;
}

.social-button.github {
  background-color: #33333379;
}

.social-button img {
  width: 18px;
  height: 18px;
  filter: invert(1);
}

.footer .copyright a {
  color: #fff;
  margin-left: 5px;
  text-decoration: underline;
  font-size: 0.9rem;
}

/* Responsividade */
@media (max-width: 600px) {
  .todo-header,
  .todo-container {
    max-width: 98vw;
    padding: 1rem;
    margin-top: 16px;
    margin-bottom: 16px;
  }
  .todo-header {
    padding: 1.2rem;
    margin-top: 24px;
    margin-bottom: 16px;
  }
  h1 {
    font-size: 2rem;
  }
  .todo-form {
    flex-direction: column;
    gap: 8px;
    max-width: 100%;
  }
  .todo-form input {
    font-size: 0.98rem;
    padding: 7px;
  }
  .todo-form button {
    font-size: 0.98rem;
    padding: 7px 0;
  }
  .todo-card {
    padding: 10px 7px;
    font-size: 0.98rem;
  }
  .timer {
    font-size: 0.92rem;
  }
  footer.footer {
    height: auto;
    padding: 10px 0 60px 0;
    font-size: 0.95rem;
  }
  .social-links {
    gap: 10px;
  }
  .social-button {
    width: 28px;
    height: 28px;
  }
  .social-button img {
    width: 15px;
    height: 15px;
  }
}

@media (max-width: 400px) {
  .todo-header,
  .todo-container {
    padding: 0.5rem;
  }
  h1 {
    font-size: 1.3rem;
  }
  .todo-form input,
  .todo-form button {
    font-size: 0.9rem;
  }
}
</style> 




