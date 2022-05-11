<script setup lang="ts">
import { ref, onMounted } from "vue";

import OpenLogin from "@toruslabs/openlogin";

// clientId can be any string for localhost
const clientId =
  "BMOvfFwXTgqnQMiHbjUcV4CTtknyaKZOKoEkUc8H8oQroww3fJIB4s387VByid9TWsGJga7-6KxLUnhvtuZPCqA";

let privKey = ref("");
let privKeyObscured = ref("");
let showingHidden = ref(true);

const openlogin = new OpenLogin({
  clientId,
  network: "testnet",
  whiteLabel: {
    name: "Helix",
  },
});

onMounted(async () => {
  await openlogin.init();

  // if openlogin instance has private key then user is already logged in
  if (openlogin.privKey) {
    console.log("User is already logged in. Private key: " + openlogin.privKey);
    privKey.value = openlogin.privKey;
    privKeyObscured.value = openlogin.privKey.replace(RegExp(/./g), "&#x2022;");
  }
});

const signInOpenLogin = async () => {
  await openlogin.login({
    loginProvider: "google",
    redirectUrl: location.href,
  });
};

const signOutOpenLogin = async () => {
  await openlogin.logout({ clientId, fastLogin: false });
  privKey.value = "";
  privKeyObscured.value = "";
};
</script>

<template>
  <button @click="signInOpenLogin" v-if="!privKey">Connect</button>
  <button @click="signOutOpenLogin" v-if="privKey">Log out</button>
  <hr v-if="privKey" />
  <div id="privKey" v-if="privKey">Your Private Key:</div>
  <div v-if="showingHidden" v-html="privKeyObscured"></div>
  <div v-if="!showingHidden">{{ privKey }}</div>
  <button v-if="privKey && showingHidden" @click="showingHidden = false">
    Show
  </button>
  <button v-if="!showingHidden" @click="showingHidden = true">Hide</button>
</template>

<style scoped></style>
