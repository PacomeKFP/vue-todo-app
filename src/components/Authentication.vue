<template>


    <div class="container center">
        <form method="POST">
            <h1 class="h3 mb-3 fw-normal"> {{ isRegistering ? "Creer votre compte" : "Connectez Vous" }} </h1>

            <div class="form-floating" v-if="isRegistering">
                <input v-model="name" class="form-control" placeholder="Votre nom ici">
                <label for="floatingInput">Nom</label>
            </div>
            <div class="form-floating" v-if="isRegistering">
                <input v-model="surname" class="form-control" placeholder="Votre prénom ici">
                <label for="floatingInput">Prénom</label>
            </div>

            <div class="form-floating">
                <input v-model="email" type="email" class="form-control" placeholder="Votre email ici">
                <label for="floatingInput">Adresse mail</label>
            </div>

            <div class="form-floating">
                <input type="password" v-model="password" class="form-control" placeholder="choisissez un mot de passe">
                <label for="floatingPassword">Password</label>
            </div>

            <button class="w-100 btn btn-lg btn-primary "
                @click.prevent="isRegistering == true ? register() : login() ">
                {{ isRegistering ? "Je créé mon compte" : "Je me connecte" }}
            </button>
            <p class="mt-5 mb-3 text-muted cursor" @click.prevent="isRegistering = !isRegistering">
                {{ isRegistering ? "me connecter avec mon compte" : "Je n'ai pas de compte: je m'inscrit" }}
            </p>
        </form>

    </div>
</template>

<script>
import axios from 'axios'
import { v4 as uuidv4 } from "uuid";
import { defineComponent } from 'vue';

// import {bcrypt} from "bcrypt"

let baseUrl = "http://localhost:3004";
export default defineComponent({
    name: "AuthenticationComponent",
    emits: ['setCurrentUser'],
    data() {
        return {
            isRegistering: false,
            name: "",
            surname: "",
            email: "",
            password: "",
            errors: []
        };
    },
    methods: {
        async register() {

            this.errors = []

            let res = await axios
                .get(`${baseUrl}/users?email=${this.email}`)
                .then((res) => res)
            let email_is_not_in_use = res.data.length == 0
            if (email_is_not_in_use === false)
                this.errors.push('Cet email est deja utilisé')

            if (this.password.trim().length < 6)
                this.errors.push('Mot de passe trop court: minimum 6 caractères')

            if (this.name.trim().length < 3 || this.surname.trim().length < 3)
                this.errors.push('Le nom et le prenom doivent tous avoir au moins 3 caracteres')


            if (this.errors.length != 0)
                return console.log(this.errors);
            // const salt = await bcrypt.genSalt();
            // hashed_password = await bcrypt.hash(this.password, salt);
            let user = {
                id: uuidv4(),
                name: this.name,
                surname: this.surname,
                email: this.email,
                password: this.password
            }

            let response = await axios.post(`${baseUrl}/users`, user).then((res) => res)
            if (response.status === 201) {

                this.$emit("setCurrentUser", user)
                return;
            }
        },

        async login() {
            this.errors = []

            if (this.password.trim().length < 6)
                return this.errors.push('Mot de passe trop court: minimum 6 caractères')

            let res = await axios
                .get(`${baseUrl}/users?email=${this.email}`)
                .then((res) => res)

            if (res.status != 200)
                return this.errors.push('Erreur interne, veuillez reessayer')

            if (res.data.length == 0)
                return this.errors.push('adresse mail invalide')

            if (res.data.length == 1 && res.data[0].password != this.password)
                return this.errors.push('Mot de passe incorecte')

            return this.$emit('setCurrentUser', res.data[0])

        }
    }
})

</script>

<style>
html,
body {
    height: 100%;
}

* {
    margin-bottom: 2px;
    padding: 0;
}

.form-signin {
    width: 100%;
    max-width: 330px;
    padding: 15px;
    margin: auto;
    color: #212121;
    border: 4px solid #ff993b;
    border-radius: 25px;
}

.form-signin .checkbox {
    font-weight: 400;
}

.form-signin .form-floating:focus-within {
    z-index: 2;
}

.form-signin input[type="email"] {
    margin-bottom: -1px;
    border-bottom-right-radius: 0;
    border-bottom-left-radius: 0;
}

.form-signin input[type="password"] {
    margin-bottom: 10px;
    border-top-left-radius: 0;
    border-top-right-radius: 0;
}
</style>

