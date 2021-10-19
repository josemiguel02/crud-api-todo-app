<template>
  <div>
    <vs-row justify="center">
      <vs-col w="5" sm="12" lg="5" type="flex" align="center" justify="center">
        <form @submit.prevent="createTodo">
          <vs-card class="card-content">
            <template #title>
              <h3 class="card-title">ToDo App</h3>
            </template>
            <template #text>
              <div class="content-inputs">
                <vs-input
                  class="input"
                  border
                  shadow
                  color="#7d33ff"
                  label-placeholder="Title"
                  v-model="newTodo.title"
                />
                <vs-input
                  class="input"
                  color="#7d33ff"
                  border
                  shadow
                  label-placeholder="Description"
                  v-model="newTodo.description"
                />

                <vs-button type="submit" block color="#3bdec8" gradient>
                  Save
                </vs-button>
              </div>
            </template>
          </vs-card>
        </form>
      </vs-col>

      <vs-col w="5" sm="12" lg="5" type="flex" align="center" justify="center">
        <vs-table class="table-content" striped>
          <template #thead>
            <vs-tr>
              <vs-th>
                Title
              </vs-th>
              <vs-th>
                Description
              </vs-th>
              <vs-th>
                Done
              </vs-th>
            </vs-tr>
          </template>
          <template #tbody>
            <vs-tr v-for="(item, i) in todos" :key="i" :data="item">
              <vs-td
                class="task-done"
                :style="[
                  { textDecoration: item.done == 'true' ? 'line-through' : '' },
                ]"
              >
                {{ item.title }}
              </vs-td>
              <vs-td
                class="task-done"
                :style="[
                  { textDecoration: item.done == 'true' ? 'line-through' : '' },
                ]"
              >
                {{ item.description }}
              </vs-td>
              <vs-td>
                <button class="checkbox" @click="() => makeDone(item, i)">
                  <h3 v-if="item.done == 'true'" class="checkbox-icon">
                    ✔
                  </h3>
                </button>
              </vs-td>

              <!-- Expand -->
              <template #expand>
                <div class="expand-content">
                  <p>
                    What do you want to do with task?
                  </p>
                  <vs-button warn gradient @click="() => modalEdit(item)">
                    Edit Task
                  </vs-button>

                  <vs-button
                    border
                    danger
                    @click="() => deleteTodo(item.id, i)"
                  >
                    Remove Task
                  </vs-button>
                </div>
              </template>
            </vs-tr>
          </template>
        </vs-table>
      </vs-col>

      <!-- Dialog edit -->
      <vs-dialog width="300px" not-center v-model="active3">
        <template #header>
          <h4 class="not-margin">Edit Task</h4>
        </template>

        <div class="con-content">
          <vs-input
            v-model="editedItem.title"
            class="input"
            border
            shadow
            warn
            @keyup.enter="editTodo(editedItem)"
            label-placeholder="Title"
          />
        </div>
        <div class="con-content">
          <vs-input
            v-model="editedItem.description"
            class="input"
            warn
            border
            shadow
            @keyup.enter="editTodo(editedItem)"
            label-placeholder="Description"
          />
        </div>
        <template #footer>
          <div class="con-footer">
            <vs-button gradient warn block @click="editTodo(editedItem)">
              Edit
            </vs-button>
            <vs-button dark transparent block @click="active3 = false">
              Cancel
            </vs-button>
          </div>
        </template>
      </vs-dialog>
    </vs-row>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data: () => ({
    todos: [],
    newTodo: {
      title: '',
      description: '',
    },
    active3: false,
    editedIndex: -1,
    editedItem: {
      title: '',
      description: '',
      done: '',
    },
    chech: true,
  }),

  methods: {
    async getTodo() {
      try {
        let request = await axios.get(
          'https://my-api-rest-flask.herokuapp.com/todos'
        )
        let data = await request.data
        this.todos.push(...data)
      } catch (error) {
        console.error(error)
      }
    },

    async createTodo() {
      const { title, description } = this.newTodo

      try {
        if (title !== '' && description !== '') {
          let createTodo = {
            title: this.newTodo.title,
            description: this.newTodo.description,
            done: 'false',
          }
          let request = await axios.post(
            'https://my-api-rest-flask.herokuapp.com/create',
            createTodo
          )
          let data = await request.data
          this.todos.push(data)
          this.openNotification('#3bdec8', 'Task saved successfully')
          this.newTodo = {}
        } else {
          this.openNotification('danger', 'Empty fields is required')
        }
      } catch (error) {
        console.error(error)
      }
    },

    async deleteTodo(id, index) {
      try {
        await axios.delete(
          `https://my-api-rest-flask.herokuapp.com/delete/${id}`
        )
        this.todos.splice(index, 1)
        this.openNotification('danger', 'Task deleted successfully')
      } catch (error) {
        console.error(error)
      }
    },

    modalEdit(item) {
      this.active3 = !this.active3
      this.editedIndex = this.todos.indexOf(item)
      this.editedItem = Object.assign({}, item)
    },

    async editTodo(item) {
      const { title, description } = this.editedItem
      try {
        if (title !== '' && description !== '') {
          const editTodo = {
            title,
            description,
            done: 'false',
          }

          const request = await axios.put(
            `https://my-api-rest-flask.herokuapp.com/edit/${item.id}`,
            editTodo
          )
          const data = await request.data
          this.active3 = !this.active3
          const index = this.todos.map(e => e.id).indexOf(item.id)
          this.todos.splice(index, 1, data)
          this.openNotification('warn', 'Task edited successfully')
        } else {
          this.openNotification('danger', 'Empty fields is required')
        }
      } catch (error) {
        console.error(error)
      }
    },

    async makeDone(item, index) {
      const { title, description, id, done } = item

      try {
        const data = {
          id,
          title,
          description,
          done: done == 'true' ? 'false' : 'true',
        }

        await axios.put(
          `https://my-api-rest-flask.herokuapp.com/edit/${id}`,
          data
        )

        this.todos.splice(index, 1, data)
      } catch (error) {
        console.error(error)
      }
    },

    openNotification(color, title) {
      this.$vs.notification({
        progress: 'auto',
        color,
        position: null,
        title,
      })
    },
  },

  created() {
    this.getTodo()
  },
}
</script>

<style>
.card-content {
  margin-top: 40px;
}

.content-inputs {
  margin: 10px;
}

.input {
  margin-bottom: 30px;
}
.table-content {
  margin-top: 40px;
}

.card-title {
  margin-top: 20px;
}

.expand-content {
  font-weight: bold;
  margin: 12px;
}

.checkbox {
  width: 30px;
  height: 30px;
  background-color: #d9d9da8f;
  border-radius: 10px;
  padding: 2;
  align-items: center;
  justify-content: center;
  display: flex;
  border: none;
  cursor: pointer;
  outline: inherit;
}

.checkbox-icon {
  color: #9f49f0;
  font-size: 18px;
  font-weight: bold;
}

.task-done {
  color: red;
  font-size: 40px;
  font-weight: 100;
}
</style>
