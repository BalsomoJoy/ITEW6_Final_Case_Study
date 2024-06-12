<template>
  <div class="container">
    <div class="row">
      <!-- Quick Links Navigation -->
      <div class="col-md-3">
        <div class="side-navigation">
          <router-link to="/dashboard" class="btn btn-primary mb-3">Home</router-link>
          <router-link to="/doctors" class="btn btn-primary mb-3">Manage Doctors</router-link>
          <router-link to="/appointments" class="btn btn-primary mb-3">View Appointments</router-link>
          <router-link to="/records" class="btn btn-primary mb-3">View Medical Records</router-link>
          <router-link to="/my-profile" class="btn btn-primary mb-3">My Profile</router-link>
        </div>
      </div>
        <div class="col-md-9">
          <div class="row justify-content-center">
            <div class="col-md-12 mt-5">
              <div class="card shadow-sm p-4">
                <h2 class="text-center mb-4">Manage Patients</h2>
                <button @click="showModal = true" class="btn btn-primary new-patient mb-3">
                  <i class="fa-solid fa-plus"></i>
                  New Patient
                </button>

                <table class="table">
                  <thead>
                    <tr>
                      <th>Complete Name</th>
                      <th>Email Address</th>
                      <th>Mobile Number</th>
                      <th>Address</th>
                      <th>Actions</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-if="patients.length === 0">
                      <td colspan="5" class="text-center">No Patient Data</td>
                    </tr>
                    <tr v-for="patient in patients" :key="patient.id">
                      <td>{{ patient.fullname }}</td>
                      <td>{{ patient.email }}</td>
                      <td>{{ patient.phone }}</td>
                      <td>{{ patient.address }}</td>
                      <td>
                        <button @click="editPatient(patient)" class="btn btn-sm btn-primary">Update</button>
                        <button @click="confirmDelete(patient.id)" class="btn btn-sm btn-danger">Delete</button>
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        </div>

        <!-- New Patient Modal -->
        <transition name="modal">
          <div v-if="showModal" class="modal-backdrop">
            <div class="modal-dialog">
              <div class="modal-content">
                <div class="modal-header">
                  <h5 class="modal-title">New Patient</h5>
                  <button type="button" class="btn-close" @click="showModal = false"></button>
                </div>
                <div class="modal-body">
                  <form @submit.prevent="createPatient">
                    <div class="row mb-3">
                      <div class="col-md-12">
                        <label for="completeName" class="form-label">Complete Name</label>
                        <input type="text" class="form-control" id="completeName" v-model="newPatient.fullname" required>
                      </div>
                    </div>
                    <div class="row mb-3">
                      <div class="col-md-6">
                        <label for="email" class="form-label">Email Address</label>
                        <input type="text" class="form-control" id="email" v-model="newPatient.email">
                      </div>
                      <div class="col-md-6">
                        <label for="phone" class="form-label">Mobile Number</label>
                        <input type="text" class="form-control" id="phone" v-model="newPatient.phone">
                      </div>
                    </div>
                    <div class="row mb-3">
                      <div class="col-md-12">
                        <label for="address" class="form-label">Address</label>
                        <input type="text" class="form-control" id="address" v-model="newPatient.address">
                      </div>
                    </div>
                    <div class="modal-footer">
                      <button type="submit" class="btn btn-primary">Submit</button>
                      <button type="button" class="btn btn-secondary" @click="showModal = false">Cancel</button>
                    </div>
                  </form>
                </div>
              </div>
            </div>
          </div>
        </transition>

        <!-- Edit Patient Modal -->
        <transition name="modal">
          <div v-if="showUpdateModal" class="modal-backdrop">
            <div class="modal-dialog">
              <div class="modal-content">
                <div class="modal-header">
                  <h5 class="modal-title">Edit Patient</h5>
                  <button type="button" class="btn-close" @click="showUpdateModal = false"></button>
                </div>
                <div class="modal-body">
                  <form @submit.prevent="updatePatient">
                    <div class="row mb-3">
                      <div class="col-md-12">
                        <label for="completeName" class="form-label">Complete Name</label>
                        <input type="text" class="form-control" id="completeName" v-model="editedPatient.fullname" required>
                      </div>
                    </div>
                    <div class="row mb-3">
                      <div class="col-md-6">
                        <label for="email" class="form-label">Email Address</label>
                        <input type="text" class="form-control" id="email" v-model="editedPatient.email">
                      </div>
                      <div class="col-md-6">
                        <label for="phone" class="form-label">Mobile Number</label>
                        <input type="text" class="form-control" id="phone" v-model="editedPatient.phone">
                      </div>
                    </div>
                    <div class="row mb-3">
                      <div class="col-md-12">
                        <label for="address" class="form-label">Address</label>
                        <input type="text" class="form-control" id="address" v-model="editedPatient.address">
                      </div>
                    </div>
                    <div class="modal-footer">
                      <button type="submit" class="btn btn-primary">Save Changes</button>
                      <button type="button" class="btn btn-secondary" @click="showUpdateModal = false">Cancel</button>
                    </div>
                  </form>
                </div>
              </div>
            </div>
          </div>
        </transition>

        <!-- Delete Confirmation Modal -->
        <transition name="modal">
          <div v-if="showDeleteModal" class="modal-backdrop">
            <div class="modal-dialog">
              <div class="modal-content">
                <div class="modal-header">
                  <h5 class="modal-title">Confirm Deletion</h5>
                  <button type="button" class="btn-close" @click="showDeleteModal = false"></button>
                </div>
                <div class="modal-body">
                  <p>Are you sure you want to delete this patient?</p>
                </div>
                <div class="modal-footer">
                  <button type="button" class="btn btn-danger" @click="deleteConfirmed">Yes</button>
                  <button type="button" class="btn btn-secondary" @click="showDeleteModal = false">Cancel</button>
                </div>
              </div>
            </div>
          </div>
        </transition>

        <!-- Toast Notification -->
        <div class="toast" :class="{ show: isToastVisible, success: isToastSuccess, error: !isToastSuccess }">{{ toastMessage }}</div>
      </div>
    </div>
</template>

<script>
import axiosInstance from '@/axiosInstance';

export default {
  data() {
    return {
      patients: [],
      showModal: false,
      showUpdateModal: false,
      editedPatient: {},
      newPatient: {
        fullname: '',
        phone: '',
        email: '',
        address: ''
      },
      isToastVisible: false,
      isToastSuccess: false,
      toastMessage: '',
      showDeleteModal: false,
      patientIdToDelete: null
    };
  },
  created() {
    this.fetchPatients();
  },
  methods: {
    async fetchPatients() {
      try {
        const response = await axiosInstance.get('/patients');
        this.patients = response.data;
        console.log(this.patients);
      } catch (error) {
        console.error('Error fetching patients', error);
      }
    },
    async deletePatient(patientId) {
      try {
        await axiosInstance.delete(`/patients/${patientId}`);
        this.patients = this.patients.filter(patient => patient.id !== patientId);
        this.showDeleteModal = false;
        this.showToast('Patient deleted successfully!', true);
      } catch (error) {
        console.error('Error deleting patient', error);
        this.showToast('Error deleting patient. Please try again.', false);
      }
    },
    confirmDelete(patientId) {
      this.showDeleteModal = true;
      this.patientIdToDelete = patientId;
    },
    async deleteConfirmed() {
      if (this.patientIdToDelete) {
        await this.deletePatient(this.patientIdToDelete);
        this.patientIdToDelete = null;
      }
    },
    async createPatient() {
      try {
        const patientData = {
          fullname: this.newPatient.fullname,
          phone: this.newPatient.phone,
          email: this.newPatient.email,
          address: this.newPatient.address,
        };
        const response = await axiosInstance.post('/patients', patientData);
        this.patients.push(response.data);
        this.newPatient = { firstName: '', lastName: '', email: '', phone: '', address: '' };
        this.showModal = false;
        this.showToast('Patient added successfully!', true);
      } catch (error) {
        console.error('Error creating patient', error);
        this.showToast('Error adding patient. Please try again.', false);
      }
    },
    async editPatient(patient) {
      this.editedPatient = { ...patient };
      this.showUpdateModal = true;
    },
    async updatePatient() {
      try {
        const response = await axiosInstance.put(`/patients/${this.editedPatient.id}`, this.editedPatient);
        const index = this.patients.findIndex(patient => patient.id === this.editedPatient.id);
        this.patients.splice(index, 1, response.data);
        this.showUpdateModal = false;
        this.showToast('Patient updated successfully!', true);
      } catch (error) {
        console.error('Error updating patient', error);
        this.showToast('Error updating patient. Please try again.', false);
      }
    },
    showToast(message, isSuccess) {
      this.toastMessage = message;
      this.isToastSuccess = isSuccess;
      this.isToastVisible = true;
      setTimeout(() => {
        this.isToastVisible = false;
      }, 3000);
    }
  }
};
</script>

<style scoped>
/* Theme Colors */
body {
  background-color: #e0e0e0;
  color: #2c3e50;
}

.card {
  border-radius: 15px;
  background: #ffffff;
  border: none;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.btn-primary {
  background: violet;
  color: #8a2be2;
  transition: color 0.3s;
}

.btn-primary:hover {
  background: #8a2be2;
  color: #fff;
}

.btn-danger {
  background-color: #e74c3c;
  border-color: #e74c3c;
}

.btn-danger:hover {
  background-color: #c0392b;
  border-color: #c0392b;
}

/* Modal styles */
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-dialog {
  background: white;
  border-radius: 15px;
  overflow: hidden;
  max-width: 500px;
  width: 100%;
  transform: translateY(-50px);
  transition: transform 0.3s ease-out, opacity 0.3s ease-out;
}

.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.4s, transform 0.1s;
}

.modal-enter-active,
.modal-leave-to /* .modal-leave-active in <2.1.8 */ {
  opacity: 0;
}

.modal-enter-active .modal-dialog,
.modal-leave-to .modal-dialog {
  transform: translateY(-10px);
}

.modal-content {
  padding: 20px;
  background: white;
  border-radius: 15px;
}

.modal-header .btn-close {
  background: none;
  border: none;
}

.btn {
  margin: 4px;
}

/* Table styles */
.table {
  border-radius: 15px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.table th,
.table td {
  padding: 12px 15px;
}

.table th {
  background-color: violet;
  color: white;
}

/* Toast styles */
.toast {
  position: fixed;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  padding: 10px 20px;
  border-radius: 5px;
  color: #fff;
  z-index: 9999;
  opacity: 0;
  transition: opacity 0.5s ease;
}

.toast.show {
  opacity: 1;
}

.toast.success {
  background-color: #27ae60;
}

.toast.error {
  background-color: #e74c3c;
}

.side-navigation {
  background-color: #f8f9fa;
  padding: 20px;
  border-radius: 15px;
}

.side-navigation .btn {
  display: block;
  width: 100%;
  margin-bottom: 10px;
}

.new-patient {
  width: 200px;
}

</style>
