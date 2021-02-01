<template>
  <div class="users">
    <div class="container">
      <section>
        <h5 class="title">Novo Usuário</h5>
        <br>
        <div class="notification">
          <h5 class="subtitle btn-success">{{success}}</h5>
          <h5 class="subtitle btn-warning">{{error}}</h5>
        </div>
        <br>
        <form @submit.prevent="createUser">
          <input type="text" placeholder="Nome" v-model="form.name">
          <input type="text" placeholder="Email" v-model="form.email">
          <button type="submit">Criar</button>
        </form>
      </section>
      <section>
        <h5 class="title">Lista de Usuários</h5>
        <ul>
          <li v-for="user in users" :key="user.id">
            <p>{{ user.name }}</p>
            <small>{{ user.email }}</small>
            <a @click="deleteUser(user.id)" class="destroy" />
          </li>
        </ul>
        </section>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import axios from '@/utils/axios'

interface User {
  id: string,
  name: string,
  email: string
}

export default defineComponent({
  data() {
    return {
      users: [] as User[],
      form: {
        name: '',
        email: ''
      },
      success: '',
      error: '',
    }
  },
  created() {
    this.fetchUsers()
  },
  methods: {
    async fetchUsers() {
      try {
        const { data } = await axios.get('/users')
        console.table(data)
        this.users = data
      } catch (error) {
        console.warn(error)
      }
    },
    async createUser() {
      const { name, email } = this.form
      try {
        const { data } = await axios.post('/user', {name, email})
        
        this.users.push(data)
        this.success = 'Usuário criado com sucesso'    
        
        setTimeout(() => {
          this.success = ''
        }, 4000)

        this.form.name = '',
        this.form.email = ''
      } catch (error) {
        this.error = 'Preencha o formulário corretamente'

        setTimeout(() => {
          this.error = ''
        }, 4000)
        console.warn(error)
      }
    },
    async deleteUser(id: User['id']) {
      try {
        await axios.delete(`/user/${id}`)

        const userIndex = this.users.findIndex(user => user.id === id)

        this.users.splice(userIndex, 1)

        this.success = 'Usuário deletado com sucesso'    
        
        setTimeout(() => {
          this.success = ''
        }, 4000)     

      } catch (error) {
        console.warn(error)
      }
    }
  }
})
</script>

<style>
.container {
  margin: 4rem auto;
  max-width: 500px;
  width: 90%;
  display: grid;
  grid-gap: 2.5rem;
}

.title {
  text-transform: uppercase;
  text-align: center;
  font-size: 2rem;
  font-weight: 500;
  margin: 0.7rem 0;
}

.subtitle {
  text-align: center;
  text-transform: uppercase;
  font-size: 0.8rem;
  font-weight: 500;
  margin: 0.7rem 0;
}

.notification {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin-bottom: 1em;
}

.btn-success {
  position: absolute;
  background-color: transparent;
  color: #2ec229;;
  border: none;
  border-radius: 1rem;
  padding: 0.6rem 1.5rem;
  width: max-content;
  transition: all 0.3s linear;
  outline: none;
}

.btn-warning {
  position: absolute;
  background-color: transparent;
  color: #ca1818;;
  border: none;
  border-radius: 1rem;
  padding: 0.6rem 1.5rem;
  width: max-content;
  transition: all 0.3s linear;
  outline: none;
}

form {
  display: grid;
  grid-gap: 1rem;
}

input {
  background: transparent;
  border: 1px solid #999fc6;
  border-radius: 1rem;
  padding: 0.6rem;
  outline: none;
  color: #e1e8ef;
}

input::placeholder {
  color: #999fc6;
}

button {
  background-color: #2d6cea;
  color: #e1e8ef;
  border: none;
  border-radius: 1rem;
  padding: 0.6rem 1.5rem;
  width: max-content;
  transition: all 0.3s linear;
  outline: none;
  cursor: pointer;
  box-shadow: 0 0 5px 3px rgba(45, 108, 234, 0.3);
}

button:hover {
  background-color: #1b5cdc;
}

p {
  margin: 0;
}

ul {
  padding: 0;
  margin: 0;
  display: grid;
  grid-gap: 1rem;
}

li {
  background-color: #2b3a4e;
  padding: 1.2rem 1rem;
  border-radius: 1rem;
  position: relative;
  list-style: none;
  color: #8b98a8;
}

.destroy {
  background-color: #d53e6b;
  width: 24px;
  height: 24px;
  border-radius: 0.5rem;
  cursor: pointer;
  transition: all 0.2s linear;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  right: 1.3rem;
}

.destroy:before,
.destroy:after {
  content: '';
  width: 3px;
  height: 13px;
  background-color: #ececf6;
  border-radius: 1rem;
  position: absolute;
  top: 50%;
  left: 50%;
}

.destroy:before {
  transform: translate(-50%, -50%) rotate(45deg);
}

.destroy:after {
  transform: translate(-50%, -50%) rotate(130deg);
}

.destroy:hover {
  background-color: #984848;
}
</style>
