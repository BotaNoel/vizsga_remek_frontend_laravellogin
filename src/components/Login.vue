<template>
  <div class="container mt-3 text-center w-50">
    <div class="card">
      <h3 class="card-title text-primary">Bejelentkezés</h3>
      <div class="card-body">
        <!-- Email Field -->
        <div class="mt-2">
          <label for="email" class="form-label">E-mail:</label>
          <input type="email" id="email" v-model="user.email" class="form-control" required autofocus />
          <p v-if="errors.email" class="text-danger mt-2">{{ errors.email }}</p>
        </div>

        <!-- Password Field -->
        <div class="mt-4">
          <label for="password" class="form-label">Jelszó:</label>
          <input type="password" id="password" v-model="user.password" class="form-control" required />
          <p v-if="errors.password" class="text-danger mt-2">{{ errors.password }}</p>
        </div>

        <!-- Remember Me Checkbox -->
        <div class="block mt-4">
          <label for="remember_me" class="inline-flex items-center">
            <input type="checkbox" id="remember_me" v-model="user.remember" class="rounded text-indigo-600" />
            <span class="ms-2 text-sm text-gray-600">Emlékezzen rám</span>
          </label>
        </div>

        <!-- Login Button -->
        <button type="button" @click="login" class="btn btn-primary mt-4">Bejelentkezés</button>

        <!-- Forgot Password Link -->
        <div class="mt-5">
          <a href="/password/request" class="text-primary">Elfelejtetted a jelszavad?</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      user: {
        email: "",
        password: "",
        remember: false,
      },
      errors: {}, // Hibaüzenetek tárolása
    };
  },
  methods: {
    async login() {
      this.errors = {};

      try {
        const response = await fetch("http://127.0.0.1:8000/api/login", {
          method: "POST",
          headers: {
            "Content-Type": "application/json"
          },
          body: JSON.stringify({
            email: this.user.email,
            password: this.user.password
          })
        });

        const data = await response.json();

        if (response.ok) {
          localStorage.setItem("token", data.token);
          localStorage.setItem("user", JSON.stringify(data.user));
          this.$router.push("/dashboard");
        } else {
          this.errors = data.errors || {};
        }
      } catch (error) {
        console.error("Bejelentkezési hiba:", error);
        alert("Hiba történt a bejelentkezés során");
      }
    }
  },
};
</script>
