<template>
  <div class="modal" v-if="isVisible">
    <div class="modal-content">
      <span class="close-button" @click="closeModal">&times;</span>
      <div class="container-fluid h-custom">
        <div class="row d-flex justify-content-center align-items-center h-100">
          <!-- Bild auf kleinen Bildschirmen ausblenden -->
          <div class="col-md-9 col-lg-6 col-xl-5 d-none d-md-block">
            <img src="@/assets/login.png" class="img-fluid" alt="Sample image">
          </div>
          <!-- Formular und Text auf kleinen Bildschirmen -->
          <div class="col-md-8 col-lg-6 col-xl-4 offset-xl-1">
            <form v-if="!isRegistering" @submit.prevent="login">
              <!-- Email input -->
              <div class="form-outline mb-4">
                <label class="form-label">Email Adresse</label>
                <input type="email" v-model="username" class="form-control form-control-lg"
                  placeholder="Adresse eingeben" required />
              </div>
              <!-- Password input -->
              <div class="form-outline mb-3">
                <label class="form-label">Passwort</label>
                <input type="password" v-model="password" class="form-control form-control-lg"
                  placeholder="Passwort" required />
              </div>
              <div class="d-flex justify-content-between align-items-center">
                <!-- Checkbox und Passwort vergessen Link -->
                <div class="form-check mb-0">
                  <input class="form-check-input me-2" type="checkbox" v-model="rememberMe" />
                  <label class="form-check-label">
                    Benutzerdaten merken
                  </label>
                </div>
                <a href="#!" class="text-body">Passwort vergessen?</a>
              </div>
              <!-- Login-Button und Link zur Registrierung -->
              <div class="text-center text-lg-start mt-4 pt-2">
                <button type="submit" class="btn btn-primary btn-lg"
                  style="width: 100%; max-width: 200px; padding-left: 2.5rem; padding-right: 2.5rem;"
                  @mouseover="startMotion" @mouseleave="endMotion">Login
                  <div class="motion-line" v-show="motionActive"></div>
                </button>
                <p class="small fw-bold mt-2 pt-1 mb-0">Noch keinen Account? <a href="#!"
                    class="link-danger" @click.prevent="showRegister">Registrieren</a></p>
              </div>
            </form>
            <!-- Registrierungsformular -->
            <form v-else @submit.prevent="register">
              <!-- Email input -->
              <div class="form-outline mb-4">
                <label class="form-label">Email Adresse</label>
                <input type="email" v-model="username" class="form-control form-control-lg"
                  placeholder="Adresse eingeben" required />
              </div>
              <!-- Password input -->
              <div class="form-outline mb-3">
                <label class="form-label">Passwort</label>
                <input type="password" v-model="password" class="form-control form-control-lg"
                  placeholder="Passwort" required />
              </div>
              <!-- Confirm Password input -->
              <div class="form-outline mb-3">
                <label class="form-label">Passwort bestätigen</label>
                <input type="password" v-model="confirmPassword" class="form-control form-control-lg"
                  placeholder="Passwort bestätigen" required />
              </div>
              <!-- Registrierungs-Button und Link zum Login -->
              <div class="text-center text-lg-start mt-4 pt-2">
                <button type="submit" class="btn btn-primary btn-lg"
                  style="width: 100%; max-width: 200px; padding-left: 2.5rem; padding-right: 2.5rem;"
                  @mouseover="startMotion" @mouseleave="endMotion">Registrieren
                  <div class="motion-line" v-show="motionActive"></div>
                </button>
                <p class="small fw-bold mt-2 pt-1 mb-0">Bereits einen Account? <a href="#!"
                    class="link-danger" @click.prevent="showLogin">Login</a></p>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ['isVisible'],
  data() {
    return {
      username: '',
      password: '',
      confirmPassword: '',
      rememberMe: false,
      isRegistering: false,
      motionActive: false
    };
  },
  methods: {
    closeModal() {
      this.$emit('close-modal');
    },
    async login() {
      try {
        const response = await fetch('http://localhost:5000/api/auth/login', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ email: this.username, password: this.password })
        });

        if (!response.ok) {
          const errorMessage = await response.text();
          alert(errorMessage);
          return;
        }

        const data = await response.json();
        console.log('Login erfolgreich', data);
        this.closeModal();
      } catch (error) {
        console.error('Fehler beim Login:', error);
      }
    },
    async register() {
      if (this.password !== this.confirmPassword) {
        alert('Passwörter stimmen nicht überein.');
        return;
      }
      try {
        const response = await fetch('http://localhost:5000/api/auth/register', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ email: this.username, password: this.password })
        });
        const data = await response.json();
        if (response.ok) {
          console.log('Registrierung erfolgreich', data);
          // Nach erfolgreicher Registrierung zum Login wechseln
          this.isRegistering = false;
          this.username = '';
          this.password = '';
          this.confirmPassword = '';
        } else {
          alert(data.message);
        }
      } catch (error) {
        console.error('Fehler bei der Registrierung:', error);
      }
    },
    showRegister() {
      this.isRegistering = true;
    },
    showLogin() {
      this.isRegistering = false;
    },
    startMotion() {
      this.motionActive = true;
    },
    endMotion() {
      this.motionActive = false;
    }
  }
};
</script>

<style scoped>
.img-fluid {
  max-width: 100%; /* Bildbreite anpassen */
  height: auto; /* Bildhöhe automatisch */
}

.modal {
  position: fixed;
  top: 10%;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 0.9rem;
}

.modal-content {
  background-color: #e5e5f8;
  padding: 2rem;
  border-radius: 10px;
  width: 90%; /* Vollständige Breite auf allen Bildschirmgrößen */
  max-width: 900px; /* Maximalbreite begrenzen */
  text-align: center;
  position: relative;
  font-size: 0.9rem;
  margin: 10px; /* Abstand hinzufügen */
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.1);
}

.text-body {
  margin-left: 50px;
  margin-right: 10px;
}

.small {
  margin-left: 20px;
}

.close-button {
  position: absolute;
  top: 4px;
  right: 10px;
  cursor: pointer;
  font-size: 2.5rem;
}

button {
  background-color: #34396E;
  color: white;
  border-radius: 25px;
  padding: 0.5rem 0.5rem;
  cursor: pointer;
  margin-top: 10px; /* Abstand zum oberen Element */
}

button:hover {
  background-color: #898ec7;
}

.form-outline {
  margin-bottom: 1.5rem;
  margin-top: 2.5rem;
}

.form-control {
  border-radius: 20px;
  padding: 1.3rem;
  font-size: 0.9rem;
  width: 100%; /* Volle Breite für alle Bildschirmgrößen */
}

.form-label {
  margin-left: 1.5rem;
  font-size: 0.9rem;
  color: #6c757d;
}

.motion-line {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 2px;
  height: 2px;
  background-color: #898ec7;
  transition: width 0.3s ease-out;
}

@media (max-width: 767.98px) {
  .modal-content {
    padding: 1rem; /* Padding reduzieren für kleinere Bildschirme */
  }

  .d-none {
    display: none !important; /* Bild auf kleinen Bildschirmen ausblenden */
  }
}
</style>
