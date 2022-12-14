<template>
    <div class="user-profile chip">
        <div class="modal fade" id="settingsModal" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1"
            aria-labelledby="staticBackdropLabel" aria-hidden="true">
            <div class="app-settings modal-dialog modal-dialog-centered modal-dialog-scrollable">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="staticBackdropLabel">
                            Paramètres d'application
                        </h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>
                    <div class="modal-body">
                        <form>
                            <div class="mb-3">
                                <label for="recipient-name" class="col-form-label">Theme d'application</label>
                                &nbsp; &nbsp;
                                <select name="theme" v-model="app_theme" id="">
                                    <option value="dark">Sombre</option>
                                    <option value="light" selected="true">Clair</option>
                                </select>
                            </div>

                            <div class="mb-3">
                                <label for="recipient-name" class="col-form-label">Langue de l'application</label>
                                &nbsp; &nbsp;
                                <select name="theme" v-model="app_language" id="">
                                    <option value="fr">Français</option>
                                    <option value="en" selected="true">Anglais</option>
                                </select>
                            </div>
                            <hr>
                            <div class="mb-3">
                                <label for="recipient-name" class="col-form-label">Nom</label>
                                <input type="text" class="form-control" @change="(userDataHasBeenModified = true)"
                                    v-model="currentUser.name" />
                            </div>

                            <div class="mb-3">
                                <label for="recipient-name" class="col-form-label">Prénom</label>
                                <input type="text" class="form-control" @change="(userDataHasBeenModified = true)"
                                    v-model="currentUser.surname" />
                            </div>



                        </form>
                    </div>
                    <div class="modal-footer ">
                        <button type="button" class="btn btn-outline-success" data-bs-dismiss="modal"
                            @click="saveSettings">
                            Sauvegarder
                            <i class="bi bi-check"> </i>
                        </button>
                        <span class="btn btn-outline-warning" @click="logout">Se déconnecter<i
                                class="bi bi-person-fill-slash "></i></span>
                        <button type="button" class="btn btn-outline-danger" data-bs-dismiss="modal"
                            @click="resetSettings">
                            Annuler
                            <i class="bi bi-slash-circle-fill"></i>
                        </button>

                    </div>
                </div>
            </div>
        </div>
        <span class=" badge badge-pill badge-success" data-bs-toggle="modal" data-bs-target="#settingsModal">

            {{ currentUser.email }} &nbsp;&nbsp; <i class="bi bi-gear"></i>

        </span>
        <span></span>
    </div>
</template>

<script>
import { defineComponent } from 'vue';
import axios from "axios";
const baseUrl = "http://localhost:3004"

export default defineComponent({
    name: "UserProfile",
    props: ['currentUser'],
    data() {
        return {
            userDataHasBeenModified: false,
            baseData: {
                currentUserName: `${this.currentUser.name}`,
                currentUserSurname: `${this.currentUser.surname}`,
                //Add lang & color here
            }
        }
    },

    methods: {
        async saveSettings() {
            //TODO: add others handlers for theme and language

            let res = await axios.put(`${baseUrl}/users/${this.currentUser.id}`, this.currentUser)

            if (res.status == 200)
                alert(`data updated successfull`)
        },

        async resetSettings() {
            console.log('baseUser', this.baseData.currentUser)

            this.currentUser.name = this.baseData.currentUserName
            this.currentUser.surname = this.baseData.currentUserSurname
            // also reset color and language here

        },
        async logout() {
            window.location.reload()
        }
    }
})
</script>

<style>
.user-profile {
    font-size: 14;
    color: #eee;
    position: absolute;
    top: 10px;
    right: 15px;
}

.app-settings {
    color: #242424;
}
</style>
