<template>
    <div class="connections">
        <div class="nav-bar">
            <router-link :to="{ name: 'swiping' }" class="logo">
                <img class="logo-image" src="./../../public/icons/peoplerlogo.jpeg">
            </router-link>
            <img class="connect-icon" src="./../../public/icons/connections_icon.png" style="width: 6em; height: 5.5em;">
            <div class="empty"></div>
        </div>
        <div class="my-connections">
            <h1>My connections</h1>
        </div>
        <div class="connect-list">
            <ul v-if="profiles.length">
                <li v-for="profile in profiles">
                    <div class="connect-element">
                        <img :src="profile.images[0]" class="connect-image"/>
                        <p>{{ profile.Info.name }}</p>
                        <div class="connect-email">{{ profile.email }}</div>
                    </div>
                </li>
            </ul>
            <div class="no-matches" v-else>
                <h4>Ouch, there seem not to be anything here...</h4>
                <img src="../../public/icons/sad_puppr.png" class="sorry">
            </div>
        </div>
    </div>
</template>
<script>
    import {db} from "../main";
    import {mapGetters} from "vuex";
    export default {
        data() {
            return {
                profiles: []
            }
        },
        created() {
            this.loadProfiles();
        },
        computed: {
            // This gets the current logged in user from the store
            ...mapGetters({
                user: "user"
            })
        },
        methods: {
            // Gets the current user from the store and its connections list
            // Loops over these ids to retrieve the corresponding profiles
            // Stores those profiles in the profiles property
            // This method gets called at creation of component
            loadProfiles: function () {
                const that = this;
                let user = this.$store.getters.user;
                let matchList = user.profile.connections;
                if (connectionList.length > 0) {
                    connectionList.forEach((item) => {
                        let currentUser;
                        db.collection("users").doc(Object.keys(item)[0]).get().then(function (doc) {
                            currentUser = doc.data();
                            if (!that.profiles.includes(currentUser))
                                that.profiles.push(currentUser);
                        });
                    });
                }
            }
        }
    };
</script>
