<template>
  <div class="dashboard">
    <h1>Welcome to the Dashboard</h1>
    <div v-if="role === 'admin'">
      <div class="row">
        <div class="col-md-4">
          <router-link to="/patients" class="nav-link">
            <div class="nav-card">
              <div class="row">
                <i class="fas fa-users nav-icon"></i>
              </div>
              <div class="row">
                <span class="nav-text">Manage Patients</span>
              </div>
            </div>
          </router-link>
        </div>
        <div class="col-md-4">
          <router-link to="/doctors" class="nav-link">
            <div class="nav-card">
              <div class="row">
                <i class="fas fa-user-md nav-icon"></i>
              </div>
              <div class="row">
                <span class="nav-text">Manage Doctors</span>
              </div>
            </div>
          </router-link>
        </div>
        <div class="col-md-4">
          <router-link to="/my-profile" class="nav-link">
            <div class="nav-card">
              <div class="row">
                <i class="fas fa-user nav-icon"></i>
              </div>
              <div class="row">
                <span class="nav-text">My Profile</span>
              </div>
            </div>
          </router-link>
        </div>
      </div>
      <div class="row">
        <div class="col-md-6 d-flex justify-content-center">
          <router-link to="/appointments" class="nav-link">
            <div class="nav-card">
              <div class="row">
                <i class="fas fa-calendar-alt nav-icon"></i>
              </div>
              <div class="row">
                <span class="nav-text">View Appointments</span>
              </div>
            </div>
          </router-link>
        </div>
        <div class="col-md-6 d-flex justify-content-center">
          <router-link to="/records" class="nav-link">
            <div class="nav-card">
              <div class="row">
                <i class="fas fa-file-medical nav-icon"></i>
              </div>
              <div class="row">
                <span class="nav-text">View Medical Records</span>
              </div>
            </div>
          </router-link>
        </div>
      </div>
    </div>
    <div v-else-if="role === 'doctor'">
      <div class="row">
        <div class="col-md-4">
          <router-link to="/appointments" class="nav-link">
            <div class="nav-card">
              <div class="row">
                <i class="fas fa-calendar-alt nav-icon"></i>
              </div>
              <div class="row">
                <span class="nav-text">Manage Appointments</span>
              </div>
            </div>
          </router-link>
        </div>
        <div class="col-md-4">
          <router-link to="/my-profile" class="nav-link">
            <div class="nav-card">
              <div class="row">
                <i class="fas fa-user nav-icon"></i>
              </div>
              <div class="row">
                <span class="nav-text">My Profile</span>
              </div>
            </div>
          </router-link>
        </div>
        <div class="col-md-4">
          <router-link to="/records" class="nav-link">
            <div class="nav-card">
              <div class="row">
                <i class="fas fa-file-medical nav-icon"></i>
              </div>
              <div class="row">
                <span class="nav-text">View Medical Records</span>
              </div>
            </div>
          </router-link>
        </div>
      </div>
    </div>
    <div v-else-if="role === 'patient'">
      <div class="row">
        <div class="col-md-4">
          <router-link to="/appointments" class="nav-link">
            <div class="nav-card">
              <div class="row">
                <i class="fas fa-calendar-alt nav-icon"></i>
              </div>
              <div class="row">
                <span class="nav-text">Book Appointments</span>
              </div>
            </div>
          </router-link>
        </div>
        <div class="col-md-4">
          <router-link to="/my-profile" class="nav-link">
            <div class="nav-card">
              <div class="row">
                <i class="fas fa-user nav-icon"></i>
              </div>
              <div class="row">
                <span class="nav-text">My Profile</span>
              </div>
            </div>
          </router-link>
        </div>
        <div class="col-md-4">
          <router-link to="/records" class="nav-link">
            <div class="nav-card">
              <div class="row">
                <i class="fas fa-file-medical nav-icon"></i>
              </div>
              <div class="row">
                <span class="nav-text">View Medical Records</span>
              </div>
            </div>
          </router-link>
        </div>
      </div>
    </div>
    <button @click="logout" class="logout-button">Logout</button>
  </div>
</template>


<script>
import axiosInstance from '@/axiosInstance';

export default {
  data() {
    return {
      role: '',
    };
  },
  created() {
    const user = JSON.parse(localStorage.getItem('user'));
    this.role = user ? user.role : '';
  },
  methods: {
    async logout() {
      try {
        await axiosInstance.post('/logout');
        localStorage.removeItem('token');
        localStorage.removeItem('user');
        delete axiosInstance.defaults.headers['Authorization'];
        this.$router.push('/login');
      } catch (error) {
        console.error('Logout failed', error);
      }
    }
  }
};
</script>

<style scoped>
.dashboard {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  background-color: #f0f2f5;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  height: 100vh;
}

h1 {
  font-size: 24px;
  margin-bottom: 20px;
}

nav {
  display: flex;
  flex-direction: column;
  gap: 10px;
  width: 100%;
  max-width: 400px;
  margin-bottom: 20px;
}

.nav-link {
  text-decoration: none;
}

.nav-card {
  border-radius: 15px;
  background: #f0f0f0;
  box-shadow: inset 4px 4px 8px #bebebe, inset -4px -4px 8px #ffffff;
  border: 1px solid #ccc;
  padding: 30px;
  text-align: center;
  transition: all 0.3s ease;
  height: 200px;
  width: 300px;
  margin: 10px;
}

.nav-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
}

.nav-icon {
  font-size: 60px;
  margin-bottom: 10px;
}

.nav-text {
  font-size: 26px;
  color: #2c3e50;
  margin-top: 10px;
}

.logout-button {
  padding: 10px 20px;
  background-color: #e74c3c;
  border: none;
  border-radius: 8px;
  color: #ffffff;
  cursor: pointer;
  transition: background-color 0.3s;
}

.logout-button:hover {
  background-color: #c0392b;
}
</style>
