<template lang="pug">
    v-container.fin-container
        //- el-header.flex-layout(:class="{ 'hidden': !showHeader }")
          img(src="~/assets/images/logo_tismart.svg")  
        v-row(:gutter="24").height-row
            v-col.fin.padding-format(:span="12")
                //- img(src="~/assets/images/logo_tismart.svg", width="50%").mt-4
                h2.title Bienvenidos
                label="Correo eléctronico"
                    v-text-field.mt-2.mb-2(placeholder="Ingrese su correo electrónico", v-model='email',:rules="emailRules")
                label= "Contraseña"
                    v-text-field.mt-2.mb-2(placeholder="Ingrese su contraseña (min 8 caracteres)", v-model='password' ,type='password')
                label= "Pregunta Secreta"
                    v-text-field.mt-2.mb-2(placeholder="Ingrese datos", v-model='question' )
                label= "Respuesta Secreta"
                    v-text-field.mt-2.mb-2(placeholder="Ingrese datos", v-model='answer')
                v-btn.btn-fin(@click='login()' :disabled="btnDisabled1", :loading="loginLoading") Ingresar
            v-col.n(:span="12")
                .grid-content.bg-skyblue
</template>

<script>
import { delay } from "q";
import encryptor from 'simple-encryptor'
export default {
  data() {
    return {
      email: "",
      password: "",
      question: "",
      answer: "",
      loginLoading: false,
      audio_stream: null,
      audio_context: null,
      recorder: null,
      key: 'real secret keys should be long and random',
      emailRules: [
        value => !!value || "Required.",
        value => (value || "").length <= 50 || "Max 50 characters",
        value => {
          const pattern = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
          return pattern.test(value) || "Invalid e-mail.";
        }
      ]
    };
  },

  head() {
    return {
      title: this.title
    };
  },
  computed: {
    btnDisabled1() {
      const pattern = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      return (
        !pattern.test(this.email) ||
        this.password.length < 3 ||
        this.question.length < 4 ||
        this.answer.length < 4
      );
    }
  },
  methods: {
    submit(data) {
      // eventBus.$emit('submitConfirm', data)
    },
    delay(ms) {
      return new Promise(res => setTimeout(res, ms));
    },
    async login() {
      // let loader = Loading.service({ fullscreen: true })
      try {
        console.log("entro a login");
        let encrypted = encryptor.encrypt(this.password);
        await this.$axios.post("/auth/signup", {
          email: this.email,
          password: password,
          question: this.question,
          answer: this.answer
        });

// Create an encryptor:

        await this.$auth
          .loginWith("local", {
            data: {
              email: this.email,
              password: encrypted
            }
          })
          .then(res => {
            console.log("res", res, this.$auth.user);
            console.log("he", this.$auth.loggedIn, this.$auth);
            this.$router.push({ name: "letters" });
          });
        // .catch(e=> console.log("Err",e))
        console.log("response ", this.$store.state.auth.user);
        //this.$store.commit('login/setUserData',user.data)
        //this.$auth.redirect('/documents')
        //this.$router.push('/documents/')
      } catch (err) {
        console.log("ERROR"); //si funciona cuando no encuentra
        // console.log(err)
        // this.$showAlert({ title: 'Credenciales/ Inválidas', message: `Correo electrónico y/o contraseña incorrecta.` })
        //   this.$refs.alertDialog.open('Error Verifica crediedenciales')
      }
      // loader.close()s
    }
  },
  mounted() {
    console.log("cargue", this.$store.state.nombre);
  },
  destroyed() {}
};
</script>

<style lang="sass">
    .fin-container
        min-height: 100vh
        .height-row
            height: 100%
        .el-col.fin
            padding: 9.5% !important
            //margin-top: 120px
            .mt-4
                padding-left: 25%
                padding-bottom: 10%
                //position: absolute
            .mt-2
                margin-top: 14px !important
            .mb-2
                margin-bottom: 14px !important
            .padding-format
                padding-top: 20%
            .bg-skyblue
                background: #8DD7F7
            .grid-content
                border-radius: 4px
                min-height: 100vh
            .btn-fin
                background-color: #68C6E8
                margin-top: 25px
                float: right
                width: 40%
                span 
                    color: white
                    text-align: center
                    font-weight: 400
        .el-col.n
            .mt-2
                margin-top: 14px !important
            .mb-2
                margin-bottom: 14px !important
            .padding-format
                padding-left: 50px
                padding-right: 30px
                padding-top: 20%
            .bg-skyblue
                background: #8DD7F7
            .grid-content
                border-radius: 4px
                min-height: 100vh
            .btn-fin
                float: right
                span 
                    color: white
                    font-weight: 400
</style>
