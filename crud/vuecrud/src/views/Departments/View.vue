<template>
  <div class="container">
    <div class="card mb-5">
      <div class ="card-header">
        <h4>
          Departments
          <RouterLink to="/departments/create" class="btn btn-primary float-end">Add Department</RouterLink>
        </h4>
      </div>
      <div class="card-body">
        <h5>Departments</h5>
        <table class="table table-bordered">
          <thead>
            <tr>
              <th>ID</th>
              <th>Name</th>
              <th>Status</th>
              <th>Date Added</th>
              <th>Action</th>
            </tr>
          </thead>
          <tbody v-if="departments.length > 0">
            <tr v-for="(department, index) in departments" :key="index">
              <td>{{ department.id }}</td>
              <td>{{ department.dept_name }}</td>
              <td>{{ department.status }}</td>
              <td>{{ department.created_at }}</td>
              <td>
                <RouterLink :to="{ path: `/departments/${department.id}/edit` }" class="btn btn-success">Edit</RouterLink>
                <button type="button" @click="deleteDepartment(department.id)" class="btn btn-danger">Delete</button>
              </td>
            </tr>
          </tbody>
          <tbody v-else>
            <tr>
              <td colspan="5">↺</td>
            </tr>
          </tbody>
        </table>

        <h5>Designations
          <RouterLink to="/designation/create" class="btn btn-primary float-end mb-2">Add Designation</RouterLink>
        </h5>
        <table class="table table-bordered">
          <thead>
            <tr>
              <th>ID</th>
              <th>Name</th>
              <th>Department ID</th>
              <th>Status</th>
              <th>Date Added</th>
            </tr>
          </thead>
          <tbody v-if="designations.length > 0">
            <tr v-for="(designation, index) in designations" :key="index">
              <td>{{ designation.id }}</td>
              <td>{{ designation.designation_name }}</td>
              <td>{{ designation.department_id }}</td>
              <td>{{ designation.status }}</td>
              <td>{{ designation.created_at }}</td>
              <td>
                <RouterLink :to="{ path: `/designation/${designation.id}/edit` }" class="btn btn-success">Edit</RouterLink>
                <button type="button" @click="deleteDesignation(designation.id)" class="btn btn-danger">Delete</button>
              </td>
            </tr>
          </tbody>
          <tbody v-else>
            <tr>
              <td colspan="5">↺</td>
            </tr>
          </tbody>
        </table>   
        <RouterLink to="/employee" class="btn btn-primary float-end">Employees</RouterLink>
      </div>
    </div>
  </div>
</template>

<script>
  import axios from 'axios'

  export default {
    name: 'DepartmentView',
    data() {
      return {
        departments: [],
        designations: []
      }
    },
    mounted() {
      this.getDepartments();
      this.getDesignations();
    },
    methods: {
      getDepartments() {
        axios.get('http://localhost:8000/api/departments')
        .then(res => {
          this.departments = res.data.departments;
        })
        .catch(error => {
          console.error('Error fetching departments:', error);
        });
      },
      getDesignations() {
        axios.get('http://localhost:8000/api/designation')
        .then(res => {
          this.designations = res.data.designations;
        })
        .catch(error => {
          console.error('Error fetching designations:', error);
        });
      },
      deleteDepartment(departmentId) {
        if (confirm('Confirm action: DELETE')) {
          axios.delete(`http://localhost:8000/api/departments/${departmentId}/delete`)
          .then(res => {
            alert(res.data.message);
            this.departments = this.departments.filter(dep => dep.id !== departmentId);
          })
          .catch(error => {
            if (error.response && error.response.status === 404) {
              alert(error.response.data.message);
            } else {
              console.error('Error deleting department:', error);
            }
          });
        }
      }, 
      deleteDesignation(designationId) {
        if (confirm('Confirm action: DELETE')) {
          axios.delete(`http://localhost:8000/api/designation/${designationId}/delete`)
          .then(res => {
            alert(res.data.message);
            this.designations = this.designations.filter(des => des.id !== designationId);
            this.getDesignations();
          })
          .catch(error => {
            if (error.response && error.response.status === 404) {
              alert(error.response.data.message);
            } else {
              console.error('Error deleting designation:', error);
            }
          });
        }
      }
    }
  }
</script>

<style scoped>
  .mb-3 {
    margin-bottom: 2rem;
  }
</style>