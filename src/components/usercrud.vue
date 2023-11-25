<template>
  <div class="container">
    <h2>User Registration</h2>
    <form @submit.prevent="formData" class="user-form">
      <div class="form-group">
        <label for="name">User name</label>
        <input
          id="name"
          type="text"
          v-model="user.name"
          class="form-control"
          placeholder="User Name"
        />
      </div>
      <div class="form-group">
        <label for="country">User country</label>
        <input
          id="country"
          type="text"
          v-model="user.country"
          class="form-control"
          placeholder="User country"
        />
      </div>

      <div class="form-group">
        <label for="phone">Phone</label>
        <input
          id="phone"
          type="text"
          v-model="user.phone"
          class="form-control"
          placeholder="Phone"
        />
      </div>
      <br />

      <button type="submit" class="btn btn-primary">Save</button>
    </form>

    <h2>User View</h2>
    <table class="user-table">
      <thead>
        <tr>
          <th scope="col">ID</th>
          <th scope="col">User Name</th>
          <th scope="col">Country</th>
          <th scope="col">Phone</th>
          <th scope="col">Option</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in result" :key="user._id">
          <td>{{ user._id }}</td>
          <td>{{ user.name }}</td>
          <td>{{ user.country }}</td>
          <td>{{ user.phone }}</td>
          <td>
            <button type="button" class="btn btn-warning" @click="edit(user)">
              Edit
            </button>
            &nbsp;
            <button type="button" class="btn btn-danger" @click="remove(user)">
              Delete
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'UserCrud',
  data() {
    return {
      result: {},
      user: {
        _id: '',
        name: '',
        country: '',
        phone: '',
      },
    }
  },
  async created() {
    await this.usersLoad()
  },
  methods: {
    async usersLoad() {
      try {
        const response = await axios.get(
          'https://crud-task-deploy.onrender.com/api/v1/users/getAll',
        )
        this.result = response.data.data
      } catch (error) {
        console.error('Error loading users:', error)
      }
    },

    async formData() {
      if (this.user._id === '') {
        await this.saveData()
      } else {
        await this.updateData()
      }
    },

    async saveData() {
      try {
        await axios.post(
          'https://crud-task-deploy.onrender.com/api/v1/users/uploadUserData',
          this.user,
        )
        alert('Data saved')
        await this.usersLoad()
      } catch (error) {
        console.error('Error saving data:', error)
      }
    },

    edit(user) {
      this.user = { ...user }
    },

    async updateData() {
      try {
        const updateDate = `https://crud-task-deploy.onrender.com/api/v1/users/updateUser/${this.user._id}`
        await axios.patch(updateDate, this.user)
        this.user = { _id: '', name: '', country: '', phone: '' }
        alert('Updated')
        await this.usersLoad()
      } catch (error) {
        console.error('Error updating data:', error)
      }
    },

    async remove(user) {
      try {
        const url = `https://crud-task-deploy.onrender.com/api/v1/users/deleteUser/${user._id}`
        await axios.delete(url)
        alert('Deleted')
        await this.usersLoad()
      } catch (error) {
        console.error('Error deleting data:', error)
      }
    },
  },
}
</script>

<style scoped>
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.user-form {
  margin-bottom: 20px;
}

.user-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

.user-table th,
.user-table td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

.user-table th {
  background-color: #4caf50;
  color: white;
}

.user-table tr:nth-child(even) {
  background-color: #f2f2f2;
}
</style>
