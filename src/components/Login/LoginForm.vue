<template>
    <v-main class="bg-img">
        <v-container fluid fill-height class="posisinya">
            <v-layout flex align-center justify-center >
                <v-flex xs12 sm6 elevation-6>
                    <v-toolbar class="green lighten-2">
                        <v-toolbar-title class="white--text">
                            <h1>Silahkan Login</h1>
                        </v-toolbar-title>
                    </v-toolbar>
                    <v-card>
                        <v-card-text class="pt-4">
                            <div>
                                <v-form v-model="valid" ref="form">
                                    <v-text-field label="E-mail" v-model="email" :rules="emailRules" required></v-text-field>
                                    <v-text-field label="Password" v-model="password" type="password" min="6" :rules="passwordRules" counter required></v-text-field>
                                    <v-layout justify-end>
                                        <v-btn text disabled class="">Belum punya akun?</v-btn>
                                        <v-btn text color="blue" @click="register"> Register </v-btn> <!-- btn go to register -->
                                        <v-spacer>  </v-spacer>
                                        <v-btn class="mr-2" @click="submit" :class="{ 'green lighten-1 white--text' : valid, disabled: !valid }"> Submit </v-btn>
                                        <v-btn @click="clear" class="white black--text"> Clear </v-btn>
                                    </v-layout>
                                </v-form>
                            </div>
                        </v-card-text>
                    </v-card>
                    <v-snackbar v-model="snackbar" :color="color" timeout="2000" bottom> {{ error_message }}</v-snackbar>
                </v-flex>
            </v-layout>
        </v-container>
    </v-main>
</template>

<style>
    .posisinya{
        position: absolute;
        left: 0;
        right: 0;
    }
    #app{
        margin-top: 0 !important;
    }
    .bg-img{
        width: 100%;
        height: 100vh;
        position: absolute;
        top: 0;
        left: 0;
        background: url('../../assets/login_back.png');
        background-size: cover;
        background-repeat: no-repeat;
    }
</style>

<script>
export default {
    name:"Login",
    data() {
        return {
            load: false,
            snackbar: false,
            error_message: '',
            color: '',
            valid: false,
            password: '',
            passwordRules: [
                (v) => !!v || 'Password tidak boleh kosong :(',
            ],
            email: '',
            emailRules: [
                (v) => !!v || 'E-mail tidak boleh kosong :(',
            ]
        };
    },
    methods: {
        submit() {
            if (this.$refs.form.validate()) {
                // validasi
                this.load = true;

                this.$http.post(this.$api + '/login', {
                    email: this.email,
                    password: this.password
                }).then(response => {
                    //simpan user
                    localStorage.setItem('id', response.data.user.id);
                    localStorage.setItem('au_first_name', response.data.user.first_name);
                    localStorage.setItem('au_last_name', response.data.user.last_name);
                    localStorage.setItem('token', response.data.access_token);
                    this.error_message = response.data.message;
                    this.color = "green";
                    this.snackbar = true;
                    this.load = false;
                    this.clear();
                    this.$router.push({
                        name: 'Home',
                    });
                }).catch(error => {
                    this.error_message = error.response.data.message;
                    this.color = "red";
                    this.snackbar = true;
                    localStorage.removeItem('token');
                    this.load = false;
                })
            }
        },

        clear() {
            this.$refs.form.reset() //clear form
        },
        
        register() {
            this.$router.push({
                name: 'Register',
            });
        }
    },
};
</script>

