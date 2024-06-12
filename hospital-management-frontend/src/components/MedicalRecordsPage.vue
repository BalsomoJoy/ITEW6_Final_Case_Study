<template>
  <div class="container">
    <!-- Quick Links Navigation -->
    <div class="row">
      <div class="col-md-3">
        <div class="side-navigation">
          <router-link to="/dashboard" class="btn btn-primary me-3">Home</router-link>
          <router-link v-if="userRole === 'admin'" to="/patients" class="btn btn-primary me-3">Manage Patients</router-link>
          <router-link v-if="userRole === 'admin'" to="/doctors" class="btn btn-primary me-3">Manage Doctors</router-link>
          <router-link v-if="userRole === 'admin' || userRole === 'doctor'" to="/appointments" class="btn btn-primary me-3">Manage Appointments</router-link>
          <router-link v-if="userRole === 'patient'" to="/appointments" class="btn btn-primary me-3">Book Appointments</router-link>
          <router-link v-if="userRole === 'admin' || userRole === 'doctor' || userRole === 'patient'" to="/my-profile" class="btn btn-primary me-3">My Profile</router-link>
        </div>
      </div>
      <div class="col-md-9">
        <div class="row justify-content-center">
          <div class="col-md-10 mt-5">
            <div class="card shadow p-4">
              <div v-if="userRole === 'admin'">
                <h3 class="text-center mb-4">All Medical Records</h3>
                <table class="table">
                  <thead>
                    <tr>
                      <th>Patient Name</th>
                      <th>Description</th>
                      <th>Doctor</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-if="records.length === 0">
                      <td colspan="3" class="text-center">No Medical Records</td>
                    </tr>
                    <tr v-for="record in records" :key="record.id">
                      <td>{{ record.patient_name }}</td>
                      <td>{{ record.description }}</td>
                      <td>{{ record.doctor }}</td>
                    </tr>
                  </tbody>
                </table>
              </div>
              <div v-else-if="userRole === 'doctor'">
                <h3 class="text-center mb-4">Manage Medical Records</h3>
                <table class="table">
                  <thead>
                    <tr>
                      <th>Patient Name</th>
                      <th>Description</th>
                      <th>Doctor</th>
                      <th>Actions</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-if="records.length === 0">
                      <td colspan="4" class="text-center">No Medical Records</td>
                    </tr>
                    <tr v-for="record in records" :key="record.id">
                      <td>{{ record.patient_name }}</td>
                      <td>{{ record.description }}</td>
                      <td>{{ record.doctor }}</td>
                      <td>
                        <button class="btn btn-sm btn-primary" @click="openUpdateRecordModal(record)">Update</button>
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>
              <div v-else-if="userRole === 'patient'">
                <h3 class="text-center mb-4">My Medical Records</h3>
                <table class="table">
                  <thead>
                    <tr>
                      <th>Doctor</th>
                      <th>Description</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-if="records.length === 0">
                      <td colspan="2" class="text-center">No Medical Records</td>
                    </tr>
                    <tr v-for="record in records" :key="record.id">
                      <td>{{ record.doctor }}</td>
                      <td>{{ record.description }}</td>
                    </tr>
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        </div>
        <!-- Update Record Modal -->
        <transition name="modal">
          <div v-if="showUpdateRecordModal" class="modal-backdrop">
            <div class="modal-dialog">
              <div class="modal-content">
                <div class="modal-header">
                  <h5 class="modal-title">Update Medical Record</h5>
                </div>
                <div class="modal-body">
                  <form @submit.prevent="updateMedicalRecord">
                    <div class="mb-3">
                      <label for="description" class="form-label">Details:</label>
                      <textarea v-model="updateRecordData.description" required class="form-control"></textarea>
                    </div>
                    <button type="submit" class="btn btn-primary">Save</button>
                    <button type="button" class="btn btn-secondary" @click="closeUpdateRecordModal">Cancel</button>
                  </form>
                </div>
              </div>
            </div>
          </div>
        </transition>
        <!-- Toast Notification -->
        <div class="toast" :class="{ show: isToastVisible, success: isToastSuccess, error: !isToastSuccess }">{{ toastMessage }}</div>
      </div>

     </div>
  </div>
</template>

<script>
import axiosInstance from '@/axiosInstance';

export default {
  name: 'MedicalRecordsPage',
  data() {
    return {
      records: [],
      userRole: '',
      isToastVisible: false,
      isToastSuccess: false,
      toastMessage: '',
      showUpdateRecordModal: false,
      updateRecordData: {
        id: '',
        description: ''
      }
    };
  },
  created() {
    this.fetchRecords();
    this.fetchUserRole();
  },
  methods: {
    async fetchRecords() {
      try {
        const response = await axiosInstance.get('/records');
        this.records = response.data;
        console.log(this.records);
      } catch (error) {
        console.error('Error fetching medical records', error);
      }
    },
    async fetchUserRole() {
      try {
        const response = await axiosInstance.get('/user/role');
        this.userRole = response.data.role;
      } catch (error) {
        console.error('Error fetching user role', error);
      }
    },
    openUpdateRecordModal(record) {
      this.updateRecordData.id = record.id;
      this.updateRecordData.description = record.description;
      this.showUpdateRecordModal = true;
    },
    closeUpdateRecordModal() {
      this.showUpdateRecordModal = false;
      this.updateRecordData = { id: '', description: '' };
    },
    async updateMedicalRecord() {
      try {
        await axiosInstance.put(`/records/${this.updateRecordData.id}`, {
          description: this.updateRecordData.description
        });
        this.showToast('Medical record updated successfully', true);
        this.closeUpdateRecordModal();
        this.fetchRecords();
      } catch (error) {
        console.error('Error updating medical record', error);
        this.showToast('Failed to update medical record', false);
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

.card {
  background-color: #ffffff;
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

.btn {
  margin: 5px;
}

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
  border-radius: 5px;
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
  border-radius: 5px;
}

.modal-header .btn-close {
  background: none;
  border: none;
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
</style>
