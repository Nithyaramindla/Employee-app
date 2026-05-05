<template>
  <div class="container mt-5">

    <div class="row justify-content-center">
      <div class="col-md-6">
        <h2 class="text-center mb-3">Employee Management</h2>

        <!-- Success Message -->
        <p v-if="message" class="alert alert-success text-center">
          {{ message }}
        </p>

        <!-- Form -->
        <form @submit.prevent="saveEmployee">
          <input v-model="emp.name" placeholder="Name" class="form-control mb-2" required>
          <input v-model="emp.designation" placeholder="Designation" class="form-control mb-2">
          <input v-model="emp.department" placeholder="Department" class="form-control mb-2">
          <input v-model="emp.salary" placeholder="Salary" class="form-control mb-3">

          <button class="btn btn-primary w-100">
            {{ editMode ? "Update Employee" : "Add Employee" }}
          </button>
        </form>
      </div>
    </div>

    <!-- Table -->
    <div class="mt-5">
      <table class="table table-hover text-center shadow">
        <thead class="table-dark">
          <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Designation</th>
            <th>Department</th>
            <th>Salary</th>
            <th>Actions</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="e in employees" :key="e.id">
            <td>#{{ e.id }}</td>
            <td>{{ e.name }}</td>
            <td>{{ e.designation }}</td>
            <td>{{ e.department }}</td>
            <td>₹ {{ e.salary }}</td>
            <td>
              <button class="btn btn-warning btn-sm me-2" @click="editEmployee(e)">
                Edit
              </button>
              <button class="btn btn-danger btn-sm" @click="deleteEmployee(e.id)">
                Delete
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

  </div>
</template>

<script>
import axios from "axios"

export default {
  data() {
    return {
      emp: {
        name: "",
        designation: "",
        department: "",
        salary: ""
      },
      employees: [],
      editMode: false,
      message: "",
      API_URL: "https://69f8f573f7044aa0103eac5e.mockapi.io/emp/employees"
    }
  },

  mounted() {
    this.getEmployees()
  },

  methods: {

    // GET all employees
    getEmployees() {
      axios.get(this.API_URL)
        .then(res => {
          this.employees = res.data
        })
        .catch(err => console.error(err))
    },

    // ADD & UPDATE
    saveEmployee() {
      if (this.editMode) {
        axios.put(`${this.API_URL}/${this.emp.id}`, this.emp)
          .then(() => {
            this.getEmployees()
            this.message = "Employee Updated Successfully ✅"
            this.resetForm()
          })
      } else {
        axios.post(this.API_URL, this.emp)
          .then(() => {
            this.getEmployees()
            this.message = "Employee Added Successfully ✅"
            this.resetForm()
          })
      }

      setTimeout(() => {
        this.message = ""
      }, 3000)
    },

    // EDIT
    editEmployee(e) {
      this.emp = { ...e }
      this.editMode = true
    },

    // DELETE
    deleteEmployee(id) {
      if (confirm("Are you sure you want to delete this employee? ⚠️")) {
        axios.delete(`${this.API_URL}/${id}`)
          .then(() => {
            this.getEmployees()
            this.message = "Employee Deleted Successfully ❌"
          })
      }

      setTimeout(() => {
        this.message = ""
      }, 3000)
    },

    // RESET FORM
    resetForm() {
      this.emp = {
        name: "",
        designation: "",
        department: "",
        salary: ""
      }
      this.editMode = false
    }

  }
}
</script>

<style>
body {
  background-color: #f8f9fa;
}
</style>