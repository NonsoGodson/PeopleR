<template>
    <div :key="hello" class="swiping" @change-profile="myMethod">
        <!-- Navigation bar that appears on the Swiping page -->
        <div class="nav-bar">
            <!-- Link to profile -->
            <router-link :to="{ name: 'myprofile' }" class="profile-icon">
                <img class="profile-icon" src="./../../public/icons/profile_icon.jpeg">
            </router-link>
            <!-- Logo that shows user is on Swiping -->
            <div class="logo" style="width: 8em; height: 5.5em;">
                <img class="logo-image" src="./../../public/icons/peoplerlogo.jpeg" style="width: 8em; height: 5.5em;">
            </div>
            <!-- Link to connections -->
            <router-link :to="{ name: 'connections' }" class="connect-icon">
                <img class="connect-icon" src="./../../public/icons/connections_icon.png">
            </router-link>
        </div>
        <!-- If a user has no connections -->
        <div class="no-connections" v-if="filteredProfiles === undefined">
            <h4>Sorry, there doesn't seem to be anything here...</h4>
            <img src="../../public/icons/disappointed_face.jpg" class="sorry">
        </div>
        <!-- Section containing the image/profile info -->
        <div class="back" :style="{ backgroundImage: 'url(' + backImage + ')'}"
             v-if="filteredProfiles">
            <div :class="transitionName">
                <transition name="flip"> <!-- the card flips when a user clicks on it -->
                    <!-- user info (likes, hobbies, bio) -->
                    <div
                            class="profileInfo"
                            v-on:click="hideInfo"
                            v-if="profileInfo && dataLoaded && filteredProfiles"
                    >
                        <h2>Bio</h2>
                        <p>{{ filteredProfiles[index].Info.bio }}</p>
                        <h2>Hobbies</h2>
                        <p>{{ filteredProfiles[index].Info.hobbies }}</p>
                        <h2>Sex</h2>
                        <p>{{ filteredProfiles[index].Info.sex }}</p>
                        <h2>Location</h2>
                        <p>{{ filteredProfiles[index].Info.location }}</p>
                    </div>
                </transition>
                <transition name="flip">
                    <!-- user image -->
                    <div
                            class="profile"
                            v-on:click="showInfo"
                            v-if="dataLoaded && !profileInfo && filteredProfiles"
                    >
                        <img
                                class="profile-img"
                                :src="filteredProfiles[index].images[0]"
                                v-if="filteredProfiles[index].images[0]"
                        />
                    </div>
                </transition>
            </div>
        </div>
        <!-- shows the name, age, and location of the user -->
        <div class="name-age" v-if="filteredProfiles && dataLoaded">
            <h1>{{ filteredProfiles[index].Info.name }}, {{ filteredProfiles[index].Info.age }}</h1>
            <h2>{{ filteredProfiles[index].city }}</h2>
        </div>
        <!-- Options to connect, pass, or rewind to the previous image -->
        <div class="action" v-if="filteredProfiles && dataLoaded">
            <img :class="realPassName" src="./../../public/icons/pass.png" @click="nextProfile"/>
            <img :class="realReverseName" src="./../../public/icons/previousicon.jpg" @click="previousProfile"/>
            <img :class="realConnectName" src="./../../public/icons/connect_icon.png" @click="connectProfile"/>
        </div>
    </div>
</template>

<script>
    import firebase from "firebase";
    import {db} from "../main";
    import {mapGetters, mapState} from "vuex";
    export default {
        // default info
        data: function () {
            return {
                hello: 0,
                index: 0,
                profileInfo: false,
                profilesList: [],
                dataLoaded: false,
                connectName: "connect",
                passName: "pass",
                reverseName: "reverse",
                transitionName: "profiles"
            };
        },
        methods: {
            showInfo() {
                if (!this.user.loggedIn) {
                    // This is code left over from anonymous browsing. This shouldn't be accessible
                    alert("You need to log in or sign up to access that feature!");
                    return;
                }
                this.profileInfo = true;
            },
            // Hide user profile info
            hideInfo() {
                this.profileInfo = false;
            },
            myMethod(idx) {
                console.log(idx);
                this.index = idx;
                this.hello++;
            },
            // Move to next user profile
            nextProfile() {
                this.passName = "shake-it";
                this.transitionName = "rotate-90-left-ccw";
                let that = this;
                setTimeout(function () {
                    // Reset list if user is at the end
                    if (that.index === that.filteredProfiles.length - 1) that.index = 0;
                    // Increment list
                    else that.index++;
                }, 500);
                let idx = this.index;
                console.log(idx);
                this.$emit("change-profile", idx);
                setTimeout(function () {
                    that.passName = "pass";
                    that.transitionName = "profiles";
                }, 500);
            },
            // Method if a user wants to connect to a mentor
            likeProfile() {
                this.likeName = "beating";
                this.transitionName = "rotate-90-right-cw";
                let that = this;
                if (!this.user.loggedIn) {
                    alert("You need to log in or sign up to access that feature!");
                    return;
                }
                // Check the other user's profile for likes
                const otherUserPro = this.filteredProfiles[this.index];
                const otherUserUid = this.filteredProfiles[this.index].user_id;
                const otherUserLikes = this.filteredProfiles[this.index].likes;
                const loggedInUid = this.user.data.localId;
                const loggedInLikes = this.user.profile.likes;
                const loggedInPro = this.user.profile;
                // Return -1 if index is not found
                let userIdx = otherUserLikes.indexOf(loggedInUid);
                // If logged in user in other user's likes then creates a new connection
                if (userIdx !== -1) {
                    // Create new connection in connection list
                    db.collection("connections")
                        .add({
                            user1: {
                                user_id: otherUserPro.user_id,
                                displayName: otherUserPro.displayName,
                                image: otherUserPro.images[0],
                                Name: otherUserPro.Info.name
                            },
                            user2: {
                                user_id: loggedInUid,
                                displayName: loggedInPro.displayName,
                                image: loggedInPro.images[0],
                                Name: loggedInPro.Info.name
                            },
                            messages: [],
                            timestamp: firebase.firestore.FieldValue.serverTimestamp()
                        })
                        .then(function (docRef) {
                            console.log("Document written with ID: ", docRef.id);
                            // Remove like from other user like list and add match object to
                            // other user's connection list
                            db.collection('users').doc(otherUserPro.user_id)
                                .update({
                                    "likes": firebase.firestore.FieldValue.arrayRemove(userIdx),
                                    "matches": firebase.firestore.FieldValue.arrayUnion({
                                        [loggedInUid]: docRef.id
                                    })
                                    // Add match object to both user's profiles connection list
                                    // key: other user's uid, value: matchId
                                }).then(function () {
                                db.collection('users').doc(loggedInUid)
                                    .update({
                                        "matches": firebase.firestore.FieldValue.arrayUnion({
                                            [otherUserUid]: docRef.id
                                        })
                                    }).then(function () {
                                    console.log("Document successfully updated and like REMOVED! from")
                                    // that.$store.dispatch('fetchUser')
                                    that.$store.dispatch("fetchProfile", that.user.data.localId)
                                    alert("You have a new connection!")
                                })
                            }).catch(function (error) {
                                console.error("Error updating like in document: ", error);
                            })
                                .catch(function (error) {
                                    console.error("Error adding document: ", error);
                                });
                        })
                } else {
                    // Add other otherUserId to loggedInUser likes
                    db.collection('users').doc(loggedInUid)
                        .update({
                            "likes": firebase.firestore.FieldValue.arrayUnion(otherUserUid)
                        }).then(function () {
                        // that.$store.dispatch('fetchUser')
                        that.$store.dispatch("fetchProfile", that.user.data.localId)
                        console.log("Document successfully updated and like ADDED!")
                    });
                }
                let idx = this.index;
                console.log(idx);
                this.$emit("change-profile", idx);
                setTimeout(function () {
                    that.likeName = "like";
                    that.transitionName = "profiles";
                }, 1000);
            },
            // Rewinds to previous profile
            previousProfile() {
                this.reverseName = "rotate";
                this.transitionName = "rotate-90-back-ccw";
                let that = this;
                if (this.index === 0) this.index = this.filteredProfiles.length - 1;
                else this.index--;
                console.log(this.index);
                let idx = this.index;
                this.$emit("change-profile", idx);
                setTimeout(function () {
                    that.reverseName = "reverse";
                    that.transitionName = "profiles";
                }, 1000);
            },
            // Filters what profiles to show a user based on location
            locationFilter() {
                let that = this;
                if (that.user.loggedIn) {
                    const retList = [];
                    const usersRef = db.collection("users");
                    let query;
                    if (!that.$store.state.user.loggedIn) {
                        query = usersRef;
                    } else {
                        query = usersRef.where(
                            "city",
                            "==",
                            that.$store.state.user.profile.city
                        );
                    }
                    query
                        .get()
                        .then(function (querySnapshot) {
                            querySnapshot.forEach(function (doc) {
                                // doc.data() is never undefined for query doc snapshots
                                let newDict = {};
                                newDict = doc.data();
                                newDict["user_id"] = doc.id;
                                retList.push(newDict);
                            });
                        })
                        .then(function () {
                            that.profilesList = retList;
                            // console.log(that.profilesList);
                            that.dataLoaded = true;
                        })
                        .catch(function (error) {
                            console.log("Error getting documents: ", error);
                        });
                    that.dataLoaded = true;
                }
            }
        },
        created() {
            this.locationFilter();
        },
        computed: {
            // Getter for user
            ...mapGetters({
                user: "user"
            }),
            // Defines and sets up state
            ...mapState({
                user: state => state.user
            }),
            backImage () {
              if (this.profileInfo)
                  return "none";
              else if (this.filteredProfiles.length > 2 && (this.transitionName === "rotate-90-right-cw" || this.transitionName === "rotate-90-left-ccw" || this.transitionName === "rotate-90-back-ccw")) {
                  let idx = this.index === this.filteredProfiles.length - 1 ? 0 : this.index + 1;
                  return this.filteredProfiles[idx].images[0];
              }
            },
            // Adds functionality such as animations for connecting to a profile
            realConnectName() {
                return this.connectName;
            },
            // Adds functionality such as animations for passing a profile
            realPassName() {
                return this.passName;
            },
            // Adds functionality such as animations for reversing a profile
            realReverseName() {
                return this.reverseName;
            },
            // Filters out a user's own profile and matched profiles
            filteredProfiles() {
                const that = this   // 'that' is a closure we need since 'this' isn't manipulatable
                let filterList = []
                if (this.profilesList.length > 0) {
                    // Filter out a user's own profile
                    filterList = this.profilesList.filter(function (item) {
                        return (item.user_id !== that.user.data.localId)
                    })
                    // Filter out profiles a user has already liked
                    filterList = filterList.filter(function (item) {
                        return !(that.user.profile.likes.includes(item.user_id))
                    })
                    // Filter out profiles a user has already connected with
                    let user = this.$store.getters.user;
                    let matchList = user.profile.matches;
                    let filterConnectionList = []
                    if (connectionList.length > 0) {
                        connectionList.forEach((item) => {
                            filterConnectionList.push(Object.keys(item)[0]);
                        })
                    }
                    filterList = filterList.filter(function (item) {
                        return !(filterConnectionList.includes(item.user_id))
                    })
                    return filterList
                }
            }
        }
    }
</script>
