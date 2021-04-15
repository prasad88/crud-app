<template>
  <div>
    <div class="jumbotron text-center">
      <h1>CRUD Operations</h1>
    </div>

    <div class="container">
      <div class="row">
        <div class="col-sm-4 border">
          <img
            src="../assets/loading.gif"
            class="img-responsive"
            width="300"
            alt="loading..."
            v-if="loading"
          />
          <div v-if="!loading">
            <h3>Add Employee</h3>
            <br />
            <div class="form-group">
              <label for="Name">Name</label>
              <input
                v-model="addNewEmployee.name"
                type="text"
                class="form-control"
                id="Name"
                placeholder="Name"
              />
            </div>
            <div class="form-group">
              <label for="City">City</label>
              <input
                v-model="addNewEmployee.city"
                type="text"
                class="form-control"
                id="City"
                placeholder="City"
              />
            </div>
            <div class="form-group">
              <label for="Phone">Phone</label>
              <input
                v-model="addNewEmployee.phone"
                type="text"
                class="form-control"
                id="Phone"
                placeholder="Phone"
              />
            </div>

            <button
              class="btn btn-primary"
              v-on:click="addEmployee()"
              style="margin-bottom: 60px"
            >
              Add
            </button>
          </div>
        </div>
        <div class="col-sm-8 border">
          <h3>All Employees</h3>
          <br />
          <table class="table table-bordered">
            <thead>
              <tr>
                <th scope="col">#</th>
                <th scope="col">Name</th>
                <th scope="col">City</th>
                <th scope="col">Phone</th>
                <th scope="col">Operation</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="employee in allEmployees" :key="employee.id">
                <th scope="row">{{ employee.id }}</th>
                <td>{{ employee.name }}</td>
                <td>{{ employee.city }}</td>
                <td>{{ employee.phone }}</td>
                <td>
                  <button
                    v-on:click="openModal(employee)"
                    class="btn btn-outline-warning btn-sm"
                  >
                    <i
                      class="fa fa-edit fa-2x"
                      style="color: #ffcc00"
                      alt="Update"
                    ></i>
                  </button>
                  <button 
                  v-on:click="delteEmployee(employee.id)"
                  class="btn btn-outline-danger btn-sm">
                    <i
                      class="fa fa-remove fa-2x"
                      style="color: #ff8080"
                      alt="Delete"
                    ></i>
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
    <div>
      <div id="app">
        <div v-if="showModal">
          <transition name="modal">
            <div class="modal-mask">
              <div class="modal-wrapper">
                <div class="modal-dialog" role="document">
                  <div class="modal-content">
                    <div class="modal-header">
                      <h5 class="modal-title">Update Employee Data</h5>
                      <button
                        type="button"
                        class="close"
                        data-dismiss="modal"
                        aria-label="Close"
                      >
                        <span aria-hidden="true" @click="showModal = false"
                          >&times;</span
                        >
                      </button>
                    </div>
                    <div class="modal-body">
                      <div class="form-group">
                        <label for="Name">Name</label>
                        <input
                          v-model="updateEmpObject.name"
                          type="text"
                          class="form-control"
                          id="Name"
                          placeholder="Name"
                        />
                      </div>
                      <div class="form-group">
                        <label for="City">City</label>
                        <input
                          v-model="updateEmpObject.city"
                          type="text"
                          class="form-control"
                          id="City"
                          placeholder="City"
                        />
                      </div>
                      <div class="form-group">
                        <label for="Phone">Phone</label>
                        <input
                          v-model="updateEmpObject.phone"
                          type="text"
                          class="form-control"
                          id="Phone"
                          placeholder="Phone"
                        />
                      </div>
                    </div>
                    <div class="modal-footer">
                      <button
                        type="button"
                        class="btn btn-secondary"
                        @click="showModal = false"
                      >
                        Close
                      </button>
                      <button
                        v-on:click="updateEmployee()"
                        type="button"
                        class="btn btn-primary"
                      >
                        Save changes
                      </button>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </transition>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  data() {
    return {
      addNewEmployee: {
        name: "",
        city: "",
        phone: "",
      },
      loading: false,
      allEmployees: [],
      showModal: false,
      updateEmpObject: {},
    };
  },
  methods: {
    addEmployee() {
      this.loading = true;
      var self = this;
      axios
        .post("http://localhost:8080/add", this.addNewEmployee)
        .then(function (response) {
          if (response.data.status) {
            self.$toast.success("Employee added!!");
            self.loading = false;
            self.addNewEmployee = {};
          } else {
            self.$toast.warning(response.data.message);
            self.loading = false;
          }
          self.loaded();
        })
        .catch(function (error) {
          self.$toast.error(error.message);
          self.loading = false;
          self.loaded();
        });
    },
    openModal(employee) {
      this.updateEmpObject = employee;
      this.showModal = true;
      console.log(this.updateEmpObject);
    },
    updateEmployee() {
      var self = this;
      axios
        .post("http://localhost:8080/updateEmployee", this.updateEmpObject)
        .then(function (response) {
          if (response.data.status) {
            self.$toast.success("Employee Updated!!");
            self.loading = false;
            self.updateEmpObject = {};
          } else {
            self.$toast.warning(response.data.message);
            self.loading = false;
          }
          self.loaded();
        })
        .catch(function (error) {
          self.$toast.error(error.message);
          self.loading = false;
          self.loaded();
        });
      this.showModal = false;
    },
    delteEmployee(id){
      var self = this;
      axios
        .get("http://localhost:8080/deleteEmployee?id="+id)
        .then(function (response) {
          self.$toast.success(response.data.message);
          self.loaded();
        })
        .catch(function (error) {
          self.$toast.error(error.message);
        });
    },
    loaded() {
      var self = this;
      axios
        .get("http://localhost:8080/getAllEmployees")
        .then(function (response) {
          self.allEmployees = response.data.data;
          console.log(JSON.stringify(self.allEmployees));
        })
        .catch(function (error) {
          console.log(error);
        });
    },
  },
  mounted() {
    this.loaded();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
i {
  padding: 10px;
}
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}
</style>
