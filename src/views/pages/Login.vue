<template>
    <header>
        <div class="container">
            <div class="header">
                <div class="header-content rounded shadow-lg p-3 mb-5 bg-white">
                    <h1 class="h2">System zgłoszeń</h1>
                    <b-tabs v-if="!this.$route.query.token" v-model="tabIndex" align="center" content-class="mt-3" class="mt-2 p-3">
                        <b-tab active>
                            <template #title>
                                <i class="fas fa-sign-in-alt"></i> Logowanie
                            </template>
                            <h2 class="h5 m-3">Zaloguj się</h2>
                            <b-alert v-if="login.status" :variant="messages[login.status].type" show>{{messages[login.status].message}}</b-alert>
                            <form @submit.prevent="submit_login">
                                <div class="form-group m-2">
                                    <label for="email">Email</label>
                                    <input type="email" name="email" v-model="login.email" class="form-control text-center" placeholder="jankowalski@example.com" autofocus autocomplete required>
                                </div>
                                <div class="form-group m-2">
                                    <label for="password">Hasło</label>
                                    <input type="password" name="password" v-model="login.password" minlength="8" class="form-control text-center" placeholder="*********" autocomplete="current-password" required>
                                </div>
                                <button v-if="loading" type="submit" class="btn btn-primary m-2 d-block float-end" disabled><Loader v-if="loading" class="d-inline-block" color="white" :size="10" sizeUnit="px" /></button>
                                <button v-else type="submit" class="btn btn-primary m-2 d-block float-end">Zaloguj</button>
                                <a href="#" class="d-block m-2 float-start" @click="tabIndex = tabIndex + 2">Odzyskaj hasło</a>
                            </form>
                        </b-tab>

                        <b-tab>
                            <template #title>
                                <i class="fas fa-user-plus"></i> Rejestracja
                            </template>
                            <h2 class="h5 m-3">Zarejestruj się</h2>
                            <b-alert v-if="register.status" :variant="messages[register.status].type" show>{{messages[register.status].message}}</b-alert>
                            <form @submit.prevent="submit_register">
                                <div class="form-group m-2">
                                    <label for="username">Nazwa</label>
                                    <input type="text" name="username" v-model="register.username" class="form-control text-center" placeholder="Jan Kowalski" autocomplete required>
                                </div>
                                <div class="form-group m-2">
                                    <label for="email">Email</label>
                                    <input type="email" name="email" v-model="register.email" class="form-control text-center" placeholder="jankowalski@example.com" autocomplete required>
                                </div>
                                <div class="form-group m-2">
                                    <label for="password">Hasło</label>
                                    <input type="password" name="password" v-model="register.password" minlength="8" class="form-control text-center" placeholder="********" autocomplete="new-password" required>
                                </div>
                                <div class="form-group m-2">
                                    <label for="password">Powtórz hasło</label>
                                    <input type="password" name="repeat_password" v-model="register.repeat_password" minlength="8" class="form-control text-center" placeholder="********" autocomplete="new-password" required>
                                </div>
                                <div class="form-group m-3">
                                    <h-captcha id="captcha_register" site-key="f9d25138-59f7-49e8-8dd0-909c605ee6f3" @verified="onCaptchaVerifiedRegister"></h-captcha>
                                </div>
                                <button v-if="loading" type="submit" class="btn btn-primary m-2 d-block float-end" disabled><Loader v-if="loading" class="d-inline-block" color="white" :size="10" sizeUnit="px" /></button>
                                <button v-else type="submit" class="btn btn-primary m-2 d-block float-end">Zarejestruj</button>                            </form>
                        </b-tab>

                        <b-tab ref="recover" title-link-class="d-none">
                            <h2 class="h5 m-3">Odzyskaj hasło</h2>
                            <b-alert v-if="recover.status" :variant="messages[recover.status].type" show>{{messages[recover.status].message}}</b-alert>
                            <form @submit.prevent="submit_recover">
                                <div class="form-group m-2">
                                    <label for="email">Email</label>
                                    <input type="email" name="email" v-model="recover.email" class="form-control text-center" placeholder="jankowalski@example.com" autocomplete required>
                                </div>
                                <div class="form-group m-3">
                                    <h-captcha id="captcha_recover" site-key="f9d25138-59f7-49e8-8dd0-909c605ee6f3" @verified="onCaptchaVerifiedRecover"></h-captcha>
                                </div>
                                <button v-if="loading" type="submit" class="btn btn-primary m-2 d-block float-end" disabled><Loader v-if="loading" class="d-inline-block" color="white" :size="10" sizeUnit="px" /></button>
                                <button v-else type="submit" class="btn btn-primary m-2 d-block float-end">Odzyskaj</button>                            
                            </form>
                        </b-tab>
                            
                    </b-tabs>

                    <div v-else>
                        <b-alert v-if="newpassword.status" :variant="messages[newpassword.status].type" show>{{messages[newpassword.status].message}}</b-alert>
                        <form @submit.prevent="submit_newpassword">
                            <div class="form-group m-2">
                                <label for="password">Nowe hasło</label>
                                <input type="password" name="password" v-model="newpassword.password" minlength="8" class="form-control text-center" placeholder="*********" autocomplete="new-password" required>
                            </div>
                            <div class="form-group m-2">
                                <label for="password">Powtórz nowe hasło</label>
                                <input type="password" name="password" v-model="newpassword.repeat_password" minlength="8" class="form-control text-center" placeholder="*********" autocomplete="new-password" required>
                            </div>
                            <button v-if="loading" type="submit" class="btn btn-primary m-2 d-block float-end" disabled><Loader v-if="loading" class="d-inline-block" color="white" :size="10" sizeUnit="px" /></button>
                            <button v-else type="submit" class="btn btn-primary m-2 d-block float-end">Zmień hasło</button>
                        </form>
                    </div>
                </div>
            </div>
        </div>
        <particles-bg class="z-index-1" type="thick" :canvas="{backgroundColor:'#888'}" :bg="true" />
    </header>
</template>

<script>
import {ParticlesBg} from "particles-bg-vue";
import {PulseLoader} from "@saeris/vue-spinners"

export default {
    name: "Login",
    components: {
        ParticlesBg,
        Loader: PulseLoader
    },
    methods: {
        submit_login: async function() {
            this.loading = true;
            if(!this.login.email || !this.login.password) return;
            const {email, password} = this.login;
            const res = await fetch("/api/user/login", {
                method: "POST",
                headers: {"Content-Type": "application/json"},
                body: JSON.stringify({email, password})
            });
            const body = await res.json();
            body.success ? this.$router.push("/panel") : this.login.status = body.error;
            return this.loading = false;
        },
        submit_register: async function() {
            this.loading = true;
            if(!this.register.username || !this.register.email || !this.register.password || !this.register.repeat_password) return;
            if(!this.register.captcha) {
                this.register.status = "EMPTY_CAPTCHA";
                return this.loading = false;
            }
            if(this.register.password !== this.register.repeat_password) {
                this.register.status = "PASSWORD_NOT_MATCH";
                document.getElementById("captcha_register").reset();
                return this.loading = false;
            }
            const {username, email, password, captcha} = this.register;
            const res = await fetch("/api/user/register", {
                method: "POST",
                headers: {"Content-Type": "application/json"},
                body: JSON.stringify({username, email, password, captcha})
            });
            const body = await res.json();
            this.register.status = body.success ? "REGISTER_SUCCESS" : body.error;
            this.register.email = null;
            this.register.username = null;
            this.register.password = null;
            this.register.repeat_password = null;
            document.getElementById("captcha_register").reset();
            return this.loading = false;
        },
        submit_recover: async function() {
            this.loading = true;
            if(!this.recover.email) return;
            if(!this.recover.captcha) {
                this.recover.status = "EMPTY_CAPTCHA";
                return this.loading = false;
            }
            const {email, captcha} = this.recover;
            const res = await fetch("/api/user/recover", {
                method: "POST",
                headers: {"Content-Type": "application/json"},
                body: JSON.stringify({email, captcha})
            });
            const body = await res.json();
            this.recover.status = body.success ? "RECOVER_SUCCESS" : body.error;
            this.recover.email = null;
            document.getElementById("captcha_recover").reset();
            return this.loading = false;
        },
        submit_newpassword: async function() {
            this.loading = true;
            if(!this.newpassword.password || !this.newpassword.repeat_password) return;
            if(this.newpassword.password !== this.newpassword.repeat_password) {
                this.newpassword.status = "PASSWORD_NOT_MATCH";
                return this.loading = false;
            }
            const res = await fetch("/api/user/newpassword", {
                method: "POST",
                headers: {"Content-Type": "application/json"},
                body: JSON.stringify({password: this.newpassword.password, token: this.$route.query.token})
            });
            const body = await res.json();
            this.newpassword.status = body.success ? "PASSWORD_CHANGE_SUCCESS" : body.error;
            this.newpassword.password = null;
            this.newpassword.repeat_password = null;
            this.loading = false;
            setTimeout(() => {
                this.$router.push("/");
            }, 800)
        },
        onCaptchaVerifiedRegister: function(e) {
            this.register.captcha = e.key;
        },
        onCaptchaVerifiedRecover: function(e) {
            this.recover.captcha = e.key;
        }
    },
    data() {
        return {
            login: {
                email: null,
                password: null,
                status: null,
            },
            register: {
                username: null,
                email: null,
                password: null,
                repeat_password: null,
                captcha: null,
                status: null
            },
            recover: {
                email: null,
                status: null
            },
            newpassword: {
                password: null,
                repeat_password: null,
                status: null
            },
            loading: false,
            tabIndex: 1,
            messages: {
                PASSWORD_NOT_MATCH: {
                    message: "Hasła się nie zgadzają.",
                    type: "danger"
                },
                INCORRECT_DATA: {
                    message: "Nie podano wszystkich danych lub są one zbyt obszerne.",
                    type: "danger"
                },
                UNKNOWN_ERROR: {
                    message: "Wystąpił nieznany błąd.",
                    type: "danger"
                },
                USER_ALREADY_EXISTS: {
                    message: "Konto z takimi danymi już jest zarejestrowane.",
                    type: "danger"
                },
                EMPTY_CAPTCHA: {
                    message: "Nie rozwiązano captcha.",
                    type: "danger"
                },
                USERNAME_TOO_SHORT: {
                    message: "Nazwa musi się składać z co najmniej 8 znaków.",
                    type: "danger"
                },
                PASSWORD_TOO_SHORT: {
                    message: "Hasło musi się składać z co najmniej 8 znaków.",
                    type: "danger"
                },
                REGISTER_SUCCESS: {
                    message: "Zarejestrowano pomyślnie. Teraz możesz się zalogować.",
                    type: "success"
                },
                WRONG_PASSWORD: {
                    message: "Podane dane logowania są nieprawidłowe.",
                    type: "danger"
                },
                RECOVER_SUCCESS: {
                    message: "Na twój email wysłana została wiadomość. Wejdź w link aby ustawić nowe hasło.",
                    type: "success"
                },
                USER_NOT_EXISTS: {
                    message: "Użytkownik o takim adresie email nie istnieje.",
                    type: "danger"
                },
                EMAIL_ERROR: {
                    message: "Wystapił problem podczas wysyłania wiadomości email.",
                    type: "danger"
                },
                PASSWORD_CHANGE_SUCCESS: {
                    message: "Pomyślnie zmieniono hasło. Za chwilę zostaniesz przekierowany do strony z logowaniem.",
                    type: "success"
                }
            }
        }
    }
}
</script>