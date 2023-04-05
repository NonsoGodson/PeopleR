<template>
    <div class="user-profile">
        <div class="nav-bar">
            <router-link :to="{ name: 'settings' }" class="setting-icon">
                <img class="setting-icon" src="./../../public/icons/settings_icon.png">
            </router-link>
            <img class="profile-icon" src="./../../public/icons/profile_icon.jpeg" style="width: 6em; height: 5.5em;">
            <router-link :to="{ name: 'swiping' }" class="logo">
                <img class="logo-image" src="./../../public/icons/peoplerlogo.jpeg">
            </router-link>
        </div>
        <div class="profile-container">
            <h1>My Profile</h1>
            <div class="my-pictures">
                <img :src="image">
                <form action="#" class="image-form">
                    <h4>Update my picture</h4>
                    <b-form-group label="" label-for="file-small" label-cols-sm="1" label-size="sm">
                        <b-form-file
                                @change="onFileChanged"
                                id="file-small"
                                size="sm">
                        </b-form-file>
                    </b-form-group>
                    <b-button type="button" @click.prevent="onUpload">Upload</b-button>
                </form>
            </div>
            <div class="user-form">
                <div class="age-name">
                    <div>
                        <h3>Name</h3>
                        <textarea v-model="Name" rows="1" :placeholder="this.user.profile.Info.name"></textarea>
                    </div>
                    <div>
                        <h3>Age</h3>
                        <textarea v-model="Age" rows="1" :placeholder="this.user.profile.Info.age"></textarea>
                    </div>
                    <div>
                        <h3>Sex</h3>
                        <textarea v-model="Sex" rows="1" :placeholder="this.user.profile.Info.sex"></textarea>
                    </div>
                </div>
                <div>
                    <h3>Hobbies</h3>
                    <textarea v-model="hobbies" rows="2" :placeholder="this.user.profile.Info.hobbies"></textarea>
                </div>
                <div>
                    <h3>Fun Facts</h3>
                    <textarea v-model="bio" rows="4" :placeholder="this.user.profile.Info.bio"></textarea>
                </div>
                <div class="save-logout">
                    <div class="submit">
                        <input type="submit" value="Save my changes" v-on:click="submit"/>
                    </div>
                    <div class="submit">
                        <router-link :to="{ name: 'login' }"><input value="Log Out" v-on:click="signOut"/></router-link>
                    </div>
                </div>
            </div>
            <div class="myspinner" v-if="spinnerOn">
                <b-spinner label="Loading..."></b-spinner>
            </div>
        </div>
    </div>
</template>
<script>
    import firebase from "firebase";
    import {db} from "../main";
    import {mapGetters} from "vuex";
    export default {
        data() {
            return {
                Name: "",
                Age: "",
                Sex: "",
                hobbies: "",
                bio: "",
                spinnerOn: false,
                selectedFile: null,
                newImage: null
            }
        },
        computed: {
            // This gets the current logged in user from the store
            ...mapGetters({
                user: "user"
            }),
            image() {
                // Select the first image of the images list for the current logged in user
                return this.user.profile.images[0];
            }
        },
        methods: {
            // Fires when the user clicks "save my changes"
            // Updates their info in the store and in the db
            submit() {
                let that = this;
                let uid = this.user.data.localId;
                this.spinnerOn = true;
                return db.collection("users")
                    .doc(uid)
                    .update({
                        "Info.name": (this.Name || this.user.profile.Info.name),
                        "Info.age": (this.Age || this.user.profile.Info.age),
                        "Info.sex": (this.Sex || this.user.profile.Info.sex),
                        "Info.hobbies": (this.likes || this.user.profile.Info.hobbies),
                        "Info.bio": (this.bio || this.user.profile.Info.bio)
                    })
                    .then(function () {
                        // Set the db and then set the store
                        that.$store.dispatch("fetchProfile", that.user.data.localId);
                        that.spinnerOn = false;
                        that.$router.replace({name: "swiping"});
                    })
                    .catch(function (error) {
                        console.error("Error writing document: ", error);
                        that.spinnerOn = false;
                    });
            },
            // Signing out
            signOut() {
                firebase
                    .auth()
                    .signOut()
                    .then(() => {
                        console.log("Logged Out");
                    });
            },
            // Sets a limit on the uploaded image size
            onFileChanged(event) {
                if (event.target.files[0].size < 4000000) {
                    this.selectedFile = event.target.files[0];
                } else {
                    alert("Image size must not be greater than 4MB");
                }
            },
            // This uploads the selected image to the db
            onUpload() {
                const that = this;
                let storageRef = firebase.storage().ref();
                let uploadTask = storageRef
                    .child(this.user.data.localId + "/" + this.selectedFile.name)
                    .put(this.selectedFile);
                // Register three observers:
                // 1. 'state_changed' observer, called any time the state changes
                // 2. Error observer, called on failure
                // 3. Completion observer, called on successful completion
                uploadTask.on(
                    "state_changed",
                    function (snapshot) {
                        // Observe state change events such as progress, pause, and resume
                        // Get task progress, including the number of bytes uploaded and the total number of bytes to be uploaded
                        let progress =
                            (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
                        console.log("Upload is " + progress + "% done");
                        that.spinnerOn = true;
                        switch (snapshot.state) {
                            case firebase.storage.TaskState.PAUSED: // or 'paused'
                                console.log("Upload is paused");
                                break;
                            case firebase.storage.TaskState.RUNNING: // or 'running'
                                console.log("Upload is running");
                                break;
                        }
                    },
                    function (error) {
                        // Handle unsuccessful uploads
                        that.spinnerOn = false;
                        alert(error);
                    },
                    function () {
                        // Handle successful uploads on complete
                        // For instance, get the download URL: https://firebasestorage.googleapis.com/...
                        uploadTask.snapshot.ref.getDownloadURL().then(function (downloadURL) {
                            // api call to store DownloadURL to user profile
                            console.log("File available at", downloadURL);
                            that.newImage = downloadURL;
                            let userRef = db.collection("users").doc(that.user.data.localId);
                            return userRef
                                .update({
                                    images:
                                        [downloadURL]
                                })
                                .then(function () {
                                    // Success of the upload
                                    that.spinnerOn = false;
                                    that.$store.dispatch("fetchProfile", that.user.data.localId)
                                })
                                .catch(function (error) {
                                    // The document probably doesn't exist.
                                    that.spinnerOn = false;
                                    console.error("Error updating document: ", error);
                                });
                        });
                    }
                );
            }
        }
    }
</script>
<style scoped>
    .myspinner {
        position: fixed;
        top: 50%;
        left: 50%;
        z-index: 100;
    }
</style>
