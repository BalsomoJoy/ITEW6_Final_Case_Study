<template>
  <div class="container">
    <div class="row">
      <!-- Side Navigation -->
      <div class="col-md-3">
        <div class="side-navigation">
          <router-link to="/dashboard" class="btn btn-primary me-3">Home</router-link>
          <router-link v-if="userRole === 'admin'" to="/patients" class="btn btn-primary me-3">Manage Patients</router-link>
          <router-link v-if="userRole === 'admin'" to="/doctors" class="btn btn-primary me-3">Manage Doctors</router-link>
          <router-link v-if="userRole === 'doctor'" to="/appointments" class="btn btn-primary me-3">Manage Appointments</router-link>
          <router-link v-if="userRole === 'patient'" to="/appointments" class="btn btn-primary me-3">Book Appointments</router-link>
          <router-link v-if="userRole === 'admin' || userRole === 'doctor' || userRole === 'patient'" to="/records" class="btn btn-primary me-3">View Medical Records</router-link>
        </div>
      </div>
      <!-- Main Content -->
      <div class="col-md-9  ">
        <div class="row justify-content-center">
          <div class="col-md-10 mt-5">
            <div class="card skeuomorphic p-4">
              <form @submit.prevent="updateUserProfile">
                <div v-if="userRole === 'admin'">
                  <h3 class="text-center mb-4">Admin Profile</h3>
                  <div class="mb-3">
                    <label for="adminName" class="form-label">Complete Name</label>
                    <input type="text" v-model="user.name" class="form-control">
                  </div>
                  <div class="mb-3">
                    <label for="adminEmail" class="form-label">Email Address</label>
                    <input type="email" v-model="user.email" class="form-control">
                  </div>
                </div>
                <div v-else-if="userRole === 'doctor'">
                  <h3 class="text-center mb-4">Doctor Profile</h3>
                  <div class="mb-3">
                    <label for="doctorName" class="form-label">Complete Name</label>
                    <input type="text" v-model="user.name" class="form-control">
                  </div>
                  <div class="mb-3">
                    <label for="doctorEmail" class="form-label">Email Address</label>
                    <input type="email" v-model="user.email" class="form-control">
                  </div>
                  <div class="mb-3">
                    <label for="specialization" class="form-label">Specialization</label>
                    <input type="text" v-model="user.specialization" class="form-control">
                  </div>
                </div>
                <div v-else>
                  <h3 class="text-center mb-4">Patient Profile</h3>
                  <div class="mb-3">
                    <label for="patientName" class="form-label">Complete Name</label>
                    <input type="text" v-model="user.name" class="form-control">
                  </div>
                  <div class="mb-3">
                    <label for="patientEmail" class="form-label">Email Address</label>
                    <input type="email" v-model="user.email" class="form-control">
                  </div>
                  <div class="mb-3">
                    <label for="phone" class="form-label">Mobile Number</label>
                    <input type="text" v-model="user.phone" class="form-control">
                  </div>
                  <div class="mb-3">
                    <label for="address" class="form-label">Address</label>
                    <textarea v-model="user.address" class="form-control"></textarea>
                  </div>
                </div>
                <button type="submit" class="btn btn-primary">Update Profile</button>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- Toast Notification -->
    <div class="toast" :class="{ show: isToastVisible, success: isToastSuccess, error: !isToastSuccess }">{{ toastMessage }}</div>
  </div>
</template>

<script>
import axiosInstance from '@/axiosInstance';

export default {
  name: 'MyProfilePage',
  data() {
    return {
      userRole: '',
      user: {
        name: '',
        email: '',
        specialization: '',
        phone: '',
        address: '',
      }, 
      isToastVisible: false,
      isToastSuccess: false,
      toastMessage: ''
    };
  },
  created() {
    this.fetchUserRole(); 
    this.fetchProfileDetails();
  },
  methods: {
    async fetchUserRole() {
      try {
        const response = await axiosInstance.get('/user/role');
        this.userRole = response.data.role; 
      } catch (error) {
        console.error('Error fetching user role', error);
      }
    },
    async fetchProfileDetails() {
      try {
        const response = await axiosInstance.get('/profile');
        const userData = response.data;
        console.log(userData);

        if (this.userRole === 'doctor') {
          this.user.name = userData.doctors.fullname;
          this.user.email = userData.doctors.email;
          this.user.specialization = userData.doctors.specialization;
        } else if (this.userRole === 'patient') {
          this.user.name = userData.patients.fullname;
          this.user.email = userData.patients.email;
          this.user.phone = userData.patients.phone;
          this.user.address = userData.patients.address;
        } else {
          this.user.name = userData.admin.name;
          this.user.email = userData.admin.email;
        }
      } catch (error) {
        console.error('Error fetching profile details', error);
      }
    },
    async updateUserProfile() {
      try {
        let fieldsToUpdate = ['name', 'email']; 

        if (this.userRole === 'doctor') {
          fieldsToUpdate.push('specialization');
        } else if (this.userRole === 'patient') {
          fieldsToUpdate.push('phone', 'address');
        }

        const userData = {};
        fieldsToUpdate.forEach(field => {
          userData[field] = this.user[field];
        });

        const response = await axiosInstance.put('/profile/update', userData);
        this.fetchProfileDetails();
        this.showToast(response.data.message, true);
      } catch (error) {
        console.error('Error updating user profile', error);
        this.showToast('Failed to update user profile', false);
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
body {
  background-color: #e0e0e0;
  color: #2c3e50;
  font-family: 'Arial', sans-serif;
}

.container-fluid {
  padding: 0;
}

.min-vh-100 {
  min-height: 100vh;
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

.card {
  border-radius: 15px;
  background: #f0f0f0;
  box-shadow: inset 4px 4px 8px #bebebe, inset -4px -4px 8px #ffffff;
  border: 1px solid #ccc;
  padding: 20px;
  width: 80%;
}

.skeuomorphic {
  border: none;
  box-shadow: inset 4px 4px 8px #bebebe, inset -4px -4px 8px #ffffff;
}

.form-control,
.form-select {
  background: #e8e8e8;
  border: none;
  box-shadow: inset 3px 3px 6px #bebebe, inset -3px -3px 6px #ffffff;
  color: #2c3e50;
}

.form-floating > label {
  color: #666;
}

.form-floating > .form-control:focus,
.form-floating > .form-select:focus {
  border: none;
  box-shadow: inset 3px 3px 6px #bebebe, inset -3px -3px 6px #ffffff;
}

.btn-primary {
  background: violet;
  color: #8a2be2;
  transition: color 0.3s;
  width: 200px;
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
</style>
