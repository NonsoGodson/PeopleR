<template>
    <div class="login">
        <div class="nav-bar">
            <div class="empty"></div>
            <div class="logo" style="width: 8em; height: 5.5em;">
                <img class="logo-image" src="./../../public/icons/peoplerlogo.jpeg" style="width: 8em; height: 5.5em;">
            </div>
            <div class="empty"></div>
        </div>
        <h1>Sign In</h1>
        <form action="#" @submit.prevent="submit">
            <div class="form-group row">
                <h2 for="email">Email</h2>
                <input
                        id="email"
                        type="email"
                        class="form-control"
                        name="email"
                        value
                        required
                        autofocus
                        v-model="form.email"
                />
            </div>
            <div class="form-group row">
                <h2 for="password">Password</h2>
                <input
                        id="password"
                        type="password"
                        class="form-control"
                        name="password"
                        required
                        v-model="form.password"
                />
            </div>
            <div>
                <button type="submit">Log In</button>
            </div>
            <div class="form-group">
                <router-link to="/signup">Don't have an account? Sign up here!</router-link>
            </div>
        </form>
        <div class="bottom-signin">
            <div class="meet-mentors">
                <img src="../../public/images/background_signup_login.jpg">
                <img src="../../public/images/background_signup_login.jpg">
            </div>
            <p>Get to learn from your mentors...</p>
        </div>
    </div>
</template>

<script>
    import firebase from "firebase";
    import { db, store } from "../main";
    export default {
        data() {
            return {
                form: {
                    email: "",
                    password: ""
                },
                error: null
            };
        },
        methods: {
            // Fires when the user logs in
            // Updates their info in the store and in the db
            submit() {
                const that = this;
                firebase
                    .auth()
                    .signInWithEmailAndPassword(this.form.email, this.form.password)
                    .then((data) => {
                        // load the profile of the user in Vuex store
                        const ref = db.collection('users').doc(data.user.uid);
                        ref.get().then(function (doc) {
                            that.$store.commit('SET_PROFILE', doc.data());
                            that.$router.replace({name: "swiping"})
                            .then(() => {
                                console.log('Successfully logged in!');
                            })
                        })
                    })
                    .catch(err => {
                        console.log(this.error = err.message);
                        alert("Incorrect login and/or password")
                    });
            }
        },
        // Makes sure that the vuex store is updated with the current user info
        // Before going to another route
        beforeRouteEnter(f, t, next) {
            next(async (vm) => {
                await this.$store.dispatch('fetchUser');
            })
        }
    };
</script>
