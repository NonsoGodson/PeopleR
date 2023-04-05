<template>
    <div class="signup">
        <transition :name="transitionName">
            <div class="signup-0" v-if="signup0">
                <div class="nav-bar">
                    <div class="empty"></div>
                    <div class="logo" style="width: 8em; height: 5.5em;">
                        <img class="logo-image" src="./../../public/icons/peoplerlogo.jpeg" style="width: 8em; height: 5.5em;">
                    </div>
                    <div class="empty"></div>
                </div>
                <h1>Sign Up</h1>
                <form action="#" @submit.prevent="submit">
                    <div class="form-group">
                        <h4 for="email">Email</h4>
                        <input type="email" required autofocus v-model="form.email"/>
                    </div>
                    <div class="form-group">
                        <h4 for="password">Password</h4>
                        <input type="password" name="password" required v-model="form.password"/>
                    </div>
                    <div class="form-group">
                        <h4 for="password">Confirm password</h4>
                        <input
                                id="password"
                                type="password"
                                class="form-control"
                                name="password"
                                required
                                v-model="form.confirmPwd"
                        />
                    </div>
                    <div class="form-group">
                        <router-link :to="{ name: 'login' }">Already have an account? Sign in here!</router-link>
                    </div>
                    <div class="form-group">
                        <button type="submit" @click="submit">Next</button>
                    </div>
                </form>
            </div>
        </transition>
        <transition :name="transitionName">
            <div class="signup-user-profile signup-1" v-if="signup1">
                <div class="nav-bar">
                    <div class="empty"></div>
                    <div class="logo" style="width: 8em; height: 5.5em;">
                        <img class="logo-image" src="./../../public/icons/peoplerlogo.jpeg" style="width: 8em; height: 5.5em;">
                    </div>
                    <div class="empty"></div>
                </div>
                <h2>My Profile</h2>
                <div class="user-form">

                    <div class="age-name">
                        <div>
                            <h4>Name</h4>
                            <textarea v-model="form.Name" rows="1" placeholder="John"></textarea>
                        </div>
                        <div>
                            <h4>Age</h4>
                            <textarea v-model="form.Age" rows="1" placeholder="20"></textarea>
                        </div>
                        <div>
                            <h4>Sex</h4>
                            <textarea v-model="form.Sex" rows="1" placeholder="Male"></textarea>
                        </div>
                        <div>
                            <h4>Location</h4>
                            <textarea v-model="form.location" rows="1" placeholder="Victoria Island"></textarea>
                        </div>
                    </div>
                    <div>
                        <h4>Hobbies</h4>
                        <textarea
                                v-model="form.hobbies"
                                rows="2"
                                placeholder="coding, playing video games..."
                        ></textarea>
                    </div>
                    <div>
                        <h4>Bio</h4>
                        <textarea v-model="form.bio" rows="4"
                                  placeholder="I love to create new apps!"></textarea>
                    </div>
                    <div class="form-group">
                        <h2>My Info</h2>
                        <div>
                            <h4>Display Name</h4>
                            <textarea required autofocus v-model="form.displayName" placeholder="JohnDoe"
                                      rows="1"></textarea>
                        </div>
                        <div>
                            <h4>City</h4>
                            <textarea required autofocus v-model="form.city" placeholder="Lagos"
                                      rows="1"></textarea>
                        </div>
                        <div>
                            <h4 for="workscope">Workscope</h4>
                            <textarea id="workscope" name="workscope" required v-model="form.workscope"
                                placeholder="Enter your career path"
                                rows="1"></textarea>
                        </div>
                    </div>
                    <div class="previous-next">
                        <button @click="goBack0">Previous</button>
                        <button @click="signUp">Next</button>
                    </div>
                </div>
            </div>
        </transition>
        <transition :name="transitionName">
            <div class="signup-2" v-if="signup2">
                <div class="nav-bar">
                    <div class="empty"></div>
                    <div class="logo" style="width: 8em; height: 5.5em;">
                        <img class="logo-image" src="./../../public/icons/peoplerlogo.jpeg" style="width: 8em; height: 5.5em;">
                    </div>
                    <div class="empty"></div>
                </div>
                <div class="upload">
                    <h1>Upload your image</h1>
                    <div class="myspinner" v-if="spinnerOn">
                        <b-spinner label="Loading..."></b-spinner>
                    </div>
                    <div class="uploaded-image" v-if="form.image">
                        <img :src="form.image">
                    </div>
                    <form action="#" class="image-form">
                        <b-form-group placeholder="Choose a file...">
                            <b-form-file id="file-large" size="lg" @change="onFileChanged"></b-form-file>
                        </b-form-group>
                        <b-button type="button" @click.prevent="onUpload">Upload</b-button>
                    </form>
                </div>
                <div class="previous-next">
                    <button @click="goBack1">Previous</button>
                    <router-link :to="{ name: 'swiping'}"><button>Get started!</button></router-link>
                </div>
            </div>
        </transition>
    </div>
</template>
<script>
    import firebase from "firebase";
    import {db} from "../main";
    import {mapGetters} from "vuex"
    export default {
        data() {
            return {
                transitionName: "slide-left",
                selectedFile: null,
                spinnerOn: false,
                // For the user auth screen
                signup0: true,
                // For the user profile screen
                signup1: false,
                // For the image upload screen
                signup2: false,
                // Form for the user's profile info
                form: {
                    email: "",
                    password: "",
                    confirmPwd: "",
                    workscope: "",
                    Name: "",
                    Age: "",
                    Sex: "",
                    location: "",
                    hobbies: "",
                    bio: "",
                    firstname: "",
                    lastname: "",
                    displayName: "",
                    city: "",
                    image: ""
                }
            };
        },
        computed: {
            // This gets the current logged in user from the store
            ...mapGetters({
                user: "user"
            })
        },
        methods: {
            // Go back from profile screen to user authentication screen
            goBack0() {
                this.transitionName = "slide-left";
                const that = this;
                setTimeout(function() {
                    that.signup0 = true;
                    that.signup1 = false;
                    }, 500);
                setTimeout(function() { that.transitionName = "slide-left";}, 1000);
            },
            // Go back from image upload screen to profile screen
            goBack1() {
                this.transitionName = "slide-right";
                const that = this;
                setTimeout(function() {
                    that.signup1 = true;
                    that.signup2 = false;
                }, 500);
                setTimeout(function() { that.transitionName = "slide-left";}, 1000);
            },
            // Creates a user with firebase auth
            submit() {
                let that = this;
                that.signup0 = false;
                that.signup1 = true;
                firebase
                    .auth()
                    .createUserWithEmailAndPassword(this.form.email, this.form.password)
                    .then(data => {
                        console.log(data.user);
                        console.log('NEXT');
                    })
                    .catch(err => {
                        console.log(err.message);
                    });
            },
            // On signing in, the store is updated with current user data
            signUp() {
                const docData = {
                    email: this.form.email,
                    workscope: this.form.workscope,
                    displayName: this.form.displayName,
                    city: this.form.city,
                    Info: {
                        name: this.form.name,
                        age: this.form.age,
                        location: this.form.location,
                        bio: this.form.bio,
                        hobbies: this.form.hobbies,
                        sex: this.form.sex
                    },
                    likes: [],
                };
                let that = this;
                let uid = this.user.data.localId;
                db.collection("users")
                    .doc(uid)
                    .set(docData)
                    .then(function () {
                        // Set the db and then set the store
                        that.$store.commit('SET_PROFILE', docData);
                        console.log("Document successfully written!");
                        that.signup1 = false;
                        that.signup2 = true;
                    })
                    .catch(function (error) {
                        console.error("Error writing document: ", error);
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
                            that.form.image = downloadURL;
                            let userRef = db.collection("users").doc(that.user.data.localId);
                            return userRef
                                .update({
                                    images:
                                        [downloadURL]
                                })
                                .then(function () {
                                    // Image was successfully uploaded
                                    that.spinnerOn = false;
                                    that.$store.dispatch("fetchProfile", that.user.data.localId);
                                    console.log("Document successfully updated!");
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
    };
</script>
<style  scoped>
    .myspinner {
        position: fixed;
        top: 50%;
        left: 50%;
        z-index: 100;
    }
</style>
