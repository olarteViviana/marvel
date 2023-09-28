<template>
  <v-app dark>
    <v-app-bar :clipped-left="clipped" fixed app  >
      
      <v-toolbar-title>
        <v-img
                class="align-end text-white"
                height="60"
                width = "200"
                src="../pages/marvel.png"
                cover
              >
        </v-img>  
      </v-toolbar-title>
      <v-spacer />
      <v-menu offset-y>
        <template #activator="{ on, attrs }">
          <v-btn color="#e92428" class="text-none" dark v-bind="attrs" v-on="on">
            Welcome
          </v-btn>
        </template>
      </v-menu>
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer :absolute="!fixed" app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  beforeMount() {
    this.loadAccount()
  },
  data() {
    return {
      fullname: "",
      clipped: true,
      drawer: true,
      fixed: false,
      miniVariant: false,

      title: 'Marvel Characters',
    }
  },
  methods: {
    closeSesion() {
      this.$router.push("/")
    },
    loadAccount() {
      const account_str = localStorage.getItem("ACCOUNT")
      if (account_str) {
        try {
          const account = JSON.parse(account_str)
          this.fullname = `${account.firstname} ${account.lastname}`
          //TODO: Validando el rol pendiente desde el API
          if (account.role == 0) {
            this.$router.push("/students/home")
          } else {
            this.$router.push("/home")
          }
        } catch (error) {
          this.$router.push("/")
        }
      } else {
        this.$router.push("/")
      }
    }
  }
}
</script>