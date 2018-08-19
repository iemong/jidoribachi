<template lang="pug">
    .login
        #firebaseui-auth-container
</template>

<script>
import * as firebase from "firebase";
import firebaseui from "firebaseui";
export default {
  mounted() {
    const ui = new firebaseui.auth.AuthUI(firebase.auth());
    ui.start("#firebaseui-auth-container", {
      signInFlow: "popup",
      signInSuccessUrl: "/#/home",
      signInOptions: [firebase.auth.EmailAuthProvider.PROVIDER_ID]
    });
  }
};
</script>

<style scoped>
</style>
