<template>
  <div>
    <UserProfile v-if="(currentUser != null)" :currentUser="currentUser" />
    <AuthenticationComponent v-if="(currentUser === null)" @setCurrentUser="setCurrentUser" />
    <TaskList v-if="(currentUser != null)" :currentUser="currentUser"/>

   </div>

</template>

<script>
import { defineComponent } from 'vue';
import AuthenticationComponent from './components/Authentication.vue';
import UserProfile from './components/UserProfile.vue';
import TaskList from './components/TaskList.vue';

export default defineComponent({
  name: 'Home Page',
  data() {
    return {
      currentUser: null,
    };
  },
  components: { AuthenticationComponent, TaskList, UserProfile },
  methods: {
    setCurrentUser(newUser) {

      this.currentUser = newUser;

    },
  },
});
</script>
