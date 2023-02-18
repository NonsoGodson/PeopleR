# PeopleR ~ Anything is possible

# Introduction
## The Project
Everybody knows the feeling of being alone, we all need friends both male and female friends. However, humans need to socialize! This is why we created PeopleR.

PeopleR is a dating site for humans! People can create their own profile and start swiping through other people's profile. If they want to meet someone, they can "like" their profile, and if that person likes them back, it's a match! From there, the two users can decide to contact each other and organize a playdate. If they don't like that person, they can just "pass" it.

Other features include: editing the person's profile, modifying the user settings, and a "previous" button to see the previous profile again.

### The Context
This project is our Portfolio Project, concluding our Foundations Year at ALX. We were able to choose who we wanted to work with and what we wanted to work on, as long as we present a working program at the end of the three weeks of development.

### The Team
We are two people enthusiasts who are passionate about coding but also like to keep it fun!

Nonso Godson Onyedumekwu, Chineze Ogugua

### Blog posts
After the development phase, we each wrote a blog post to reflect on the PeopleR journey.

Marc's article: PeopleR: It’s Like Tinder 
Drew's article: PeopleR: The social app for people
Laura's article: PeopleR: Learnings from building a dating site for people
Tutorial
Take a tour of the deployed version at people.best
-> PeopleR

Here is a little preview of our main feature, the swiping through other people's profiles:

Run PeopleR with Vue-CLI
Installing the programs necessary to view this project is pretty simple!

We'll be using npm to install Vue and Vue-CLI. First clone this repo, then navigate to the root and install Vue by executing this command: PeopleR$ npm install vue

Once that has finished, install Vue-CLI with this command: PeopleR$ npm install -g @vue/cli

In case there are any missing dependencies, please execute PeopleR$ npm install to get them. If there's an error, it should return the specific command you need to enter.

Once this is all done you're ready to run PeopleR! Still in the root of this directory, simply execute PeopleR$ npm run serve and give it a few seconds to get started. Once it's up, you can open your web browser and enter localhost:8080. This will allow you to try out PeopleR!

When you are finished simply go back to your terminal and hit ctrl + c to quit the program.

### Known bugs
Some transitions are not as fluid as expected, and due to API calls lag can be a bit off.
Issue when viewing on mobile. Many of the assets become squashed vertically.

### Overview
Our web app is a single-page app, coded mainly in Javascript. PeopleR is front-end heavy, meaning that we focused our time and energy in developing a simple but easy to use and fun app. We designed most of the User Interface, using plain CSS and some native Vue transitions and animations. We also incorporated some BootstrapVue elements which offered a simple solution for some features like image uploading.

### Vue.js
For this project, we decided to focus on learning a new front-end framework. Following the advice of mentors and professionals, we chose to learn and use Vue.js.

Every different section of the app is a Vue component, and all the components can be found in the directory src/components/. The main component "App" is defined in App.vue, and is the entry point of the app.

All the components are linked together thanks to a VueRouter instance, defined in index.js. Each component is linked to a route, which path is appended automatically at the end of our URL.

The main.js file contains the instanciation of the Vue for the entire app, as well as the config options, database session and authentication session.

Another interesting point about Vue.js is that it allowed us to use a store, defined in store.js. This store is a front-end store that keeps track of the state of components and data throughout the app. This is were the data from our database requests is stored and updated before going back in the database. This store also allows to not pass properties from each component to all its children components, and to access data from anywhere without having to use an event bus.

### List of components
These components make up what a user experiences when they check out PeopleR. Each component contains the code for a specific page of the app. These components can be located in src/components.

### Component	and Description
Landing.vue - The landing page a user sees when they navigate to PeopleR.
Login.vue - The login page. There's a link to go to the Signup page if a user hasn't signed up.
Matches.vue - Page where users can see the other users they've matched with. A match occurs when two users have liked each other.
Navbar.vue - The navigation bar that appears at the top of most every other component
Settings.vue - Users can change their email address, display name, city, and zip code on this page.
Signup.vue - Signup page for users who do not have an account. It asks for a valid email address and for them to make and confirm a password.
Swiping.vue - The main page of PuppR where users can see another user's profile and choose whether to 'like' or 'pass.'
UserProfile.vue - Similar to Settings.vue, on this page the user can change their information including likes, dislikes, and fun facts.
Firebase
We decided to go with Firebase for our backend/database as it provides all the functionality we need to develop this project such as authentication, database storage, and cloud storage. As a non-relational database, it made calling any required information simple.

Firebase was incredibly helpful while we were testing as it allowed us to look through each user's collection of information. This was helpful while we were implementing the logic for user likes and matches.

### Firestore
Since our app requires frequent and numerous database calls, Firestore provides a reliable and responsive solution to achieve a seamless experience. Practically all user information, such as display name, personal info, and even their like/match lists are kept in the Firestore. This lets us quickly populate each vue with relevant information.

For instance, when a user logs in they'll be taken to the main Swiping component where another user's image, name, age, and location will be displayed. They can choose to 'like' or 'pass' the person, which will immediately populate with another person's information. Firebase's quick database calls populate this information and help keep this functionality snappy.

Images are kept in the Firestore as a link to where they're held in cloud storage.

### Authentication
As our app connects people with each other, authentication is a necessity. Firebase provides a straightforward and easy-to-implement solution so we can focus on designing an accessible app. Users simply sign up with an existing email address and a password of their choice. Firebase Authentication does the heavy lifting to make sure users are authentic.

### Cloud Firestore
The obvious choice for storing people's photos. It provides straightforward implementation for users to upload their photo and a relatively quick way to call and display these images for users to sift through.

### Acknowledgments
ALX School staff - For the help, advice and resources they provided us with during this project and during all our curriculum.

Cohort 8 and all ALX students - For your friendship, invaluable support, and insight not only for this project, but over the last year.

Romain Bonhomme - For his incredible Vue.js knowledge and overall front-end good practices.

Valentin Roudge - For his help and advice in Node.js and architecture best practices.

YOU - For reading this documentation and testing out PeopleR. We hope you enjoyed the ride!

Related projects
AirBnB Clone: a simple web app made in Python, Flask, and JQuery.

Simple Shell: a command line interpreter that replicates the sh program.
