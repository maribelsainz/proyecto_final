<template>
  <div class="login">
    <h1 class="login__titulo p-5">{{login_titulo}}</h1>
    <div class="alert alert-danger" role="alert" v-if="login_error">{{login_error}}</div>
    <b-row class="my-1 login__grid">
      <b-form-input
        type="email"
        v-model="login_email"
        class="mb-3"
        id="email"
        placeholder="tengo.hambre@gmail.com"
      ></b-form-input>
      <b-form-input
        type="password"
        v-model="login_pass"
        class="mb-3"
        id="password"
        placeholder="tu password"
      ></b-form-input>
    </b-row>
    <button
      type="button"
      class="btn btn-success login__boton mb-5 mt-2"
      @keyup.enter="login"
      @click.prevent="login"
    >{{login_boton}}</button>
    <h3 class="login__textoIrRegistro">{{texto_irRegistro}}</h3>
    <button
      type="button"
      class="btn btn-success login__boton-irRegistro mb-5 w-25"
      @click="irRegistro"
    >{{titulo_boton_irRegistro}}</button>
  </div>
</template>
<script>
import firebase from "firebase";

export default {
  name: "Login",
  data() {
    return {
      login_titulo: "Login",
      login_boton: "Ingresar",
      login_email: "",
      login_pass: "",
      login_error: "",
      texto_irRegistro: "¿No te has registrado?",
      titulo_boton_irRegistro: "Ir a Registro!"
    };
  },
  methods: {
    login() {
      if (!this.login_email && !this.login_pass) {
        this.login_error = "Debe ingresar los datos del usuario";
        setTimeout(() => {
          this.login_error = "";
        }, 3000);
      } else if (this.login_pass.length < 6) {
        this.login_error = "La contraseña debe ser mayor a 6 digitos";
        setTimeout(() => {
          this.login_error = "";
        }, 3000);
      } else {
        console.log("entro al login");
        firebase
          .auth()
          .signInWithEmailAndPassword(this.login_email, this.login_pass)
          .then(() => {
            this.$router.replace({ name: "Busqueda" });
          })
          .catch(error => {
            console.error(error);
            if (error.code == "auth/user-not-found") {
              this.login_error =
                "El usuario no existe en nuestra base de datos";
              //   Se limpian las variables reiniciándolas:
              this.login_pass = "";
              this.login_email = "";
              setTimeout(() => {
                this.login_error = "";
              }, 3000);
            } else if (error.code == "auth/wrong-password") {
              this.login_error =
                "La contraseña no es válida o el usuario no tiene una contraseña.";
              this.login_pass = "";
              this.login_email = "";
              setTimeout(() => {
                this.login_error = "";
              }, 3000);
            } else {
              alert(error.message);
              console.log("no entro");
              this.error = error.message;
            }
          });
      }
    },
    irRegistro() {
      this.$router.push("/registro");
    }
  }
};
</script>
<style>
.login {
  margin: 4%;
  background: rgba(00, 00, 0, 0.8);
}
.login__titulo {
  color: white;
  opacity: 1 !important;
  font-family: "Amatic SC", cursive;
  font-size: 60px;
}
.login__boton {
  font-family: "Amatic SC", cursive;
  font-size: 40px !important;
}
.login__grid {
  display: grid !important;
  justify-content: center;
}
.login__textoIrRegistro {
  font-family: "Avenir";
  font-size: 20px !important;
  color: white;
}
.login__boton-irRegistro {
  font-family: "Amatic SC", cursive;
  font-size: 25px !important;
}
</style>