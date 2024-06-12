<template>
  <div class="container-fluid bg-page">
    <div class="row min-vh-100">
      <div class="col-md-6 d-flex align-items-center justify-content-center">
        <div class="card skeuomorphic p-4">
          <form @submit.prevent="register">
            <h2 class="text-center mb-4">Patient Registration</h2>
            <div class="row mb-3">
              <div class="col-md-12">
                <div class="form-floating">
                  <input v-model="fullName" id="full_name" type="text" class="form-control" placeholder="Enter your complete name" required>
                  <label for="full_name">Complete Name</label>
                </div>
              </div>
            </div>
            <div class="row mb-3">
              <div class="col-md-12">
                <div class="form-floating">
                  <input v-model="email" id="email" type="email" class="form-control" placeholder="Enter your email" required>
                  <label for="email">Email Address</label>
                </div>
              </div>
            </div>
            <div class="row mb-3">
              <div class="col-md-5 position-relative">
                <div class="form-floating">
                  <input :type="showPassword ? 'text' : 'password'" v-model="password" id="password" class="form-control" placeholder="Enter your password" required>
                  <label for="password">Password</label>
                </div>
              </div>
              <div class="col-md-5">
                <div class="form-floating">
                  <input :type="showPassword ? 'text' : 'password'" v-model="password_confirmation" id="password_confirmation" class="form-control" placeholder="Confirm your password" required>
                  <label for="password_confirmation">Confirm Password</label>
                </div>
              </div>
              <div class="col-md-2 d-flex align-items-center justify-content-center">
                <div class="form-floating">
                  <i class="fas" :class="showPassword ? 'fa-eye-slash' : 'fa-eye'" @click="toggleShowPassword" style="cursor: pointer;"></i>
                </div>
              </div>
            </div>
            <button type="submit" class="btn btn-violet w-100 mb-3">Register</button>
            <div class="line"><hr>or<hr></div>
            <router-link to="/login" class="d-block text-center router-link-custom">Already have an account? Login here.</router-link>
          </form>
        </div>
      </div>
      <div class="col-md-6 d-none d-md-block p-0">
        <!-- <img src="@/assets/bg.png" class="img-fluid full-height-img" alt="Background Image"> -->
      </div>
    </div>
    <div class="toast" :class="{ show: isToastVisible, success: isToastSuccess }">{{ toastMessage }}</div>
  </div>
</template>
  
<script>
  import axiosInstance from '@/axiosInstance';

  export default {
    data() {
      return {
      fullName: '',
      email: '',
      password: '',
      password_confirmation: '',
      role: '',
      showPassword: false,
      isToastVisible: false,
      isToastSuccess: false,
      toastMessage: ''
      };
    },
    methods: {
      async register() {
        try {
          const response = await axiosInstance.post('/register', {
          name: this.fullName,
          email: this.email,
          password: this.password,
          password_confirmation: this.password_confirmation,
          });
          this.toastMessage = response.message;
          this.isToastSuccess = true;
          this.showToast("Registration Successful", true);
          setTimeout(() => {
              this.$router.push('/login');
          }, 500);
        } catch (error) {
          console.error('Registration failed', error);
          this.showToast('Registration failed. Please try again.', false);
        }
      },
      showToast(message, isSuccess) {
        this.toastMessage = message;
        this.isToastSuccess = isSuccess;
        this.isToastVisible = true;
        setTimeout(() => {
          this.isToastVisible = false;
        }, 3000);
      },
      toggleShowPassword() {
        this.showPassword = !this.showPassword;
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

.card {
  border-radius: 15px;
  background: #f0f0f0;
  box-shadow: inset 4px 4px 8px #bebebe, inset -4px -4px 8px #ffffff;
  border: 1px solid #ccc;
  padding: 20px;
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

.btn-glass {
  background: #1e90ff;
  border: none;
  color: #ffffff;
  border-radius: 10px;
  padding: 10px 20px;
  box-shadow: 3px 3px 8px #bebebe, -3px -3px 8px #ffffff;
  transition: background 0.3s, box-shadow 0.3s;
}

.btn-glass:hover {
  background: #1c86ee;
  box-shadow: inset 3px 3px 8px #bebebe, inset -3px -3px 8px #ffffff;
}

.router-link-custom {
  color: violet;
  transition: color 0.3s;
}

.router-link-custom:hover {
  color: darkviolet;
}

.line {
  display: flex;
  padding: 0 10px;
  align-items: center;
}

hr {
  flex-grow: 1;
  border: none;
  border-top: 1px solid #0A4D68;
  margin: 0 10px;
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

.full-height-img {
  height: 100vh;
  object-fit: cover;
}

.bg-page {
  background-image: url('../assets/bg.png');
  background-size: cover;
  background-position: center;
  height: 100vh;
  width: 100%;
  overflow: hidden;
}

.d-flex {
  display: flex !important;
}

.align-items-center {
  align-items: center !important;
}

.justify-content-center {
  justify-content: center !important;
}
.btn-violet {
  background-color: violet;
  border-color: violet;
  color: #fff;
}

.btn-violet:hover {
  background-color: darkviolet;
  border-color: darkviolet;
}
</style>

