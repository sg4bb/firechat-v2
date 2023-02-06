<script setup>
import { ref, inject, nextTick, watchEffect } from "vue";
import {
  collection,
  query,
  where,
  onSnapshot,
  orderBy,
} from "firebase/firestore";
import { db, auth } from "../firebase";

const userGoogle = inject("userGoogle");
const messages = ref([]);
const chatRef = ref(null);

watchEffect((onCleanup) => {
  if (userGoogle.value) {
    const q = query(collection(db, "chats"), orderBy("time"));
    const unsubscribe = onSnapshot(q, async (snapshot) => {
      snapshot.docChanges().forEach(async (change) => {
        if (change.type === "added") {
          // console.log("New chat: ", change.doc.data());
          messages.value.push({
            id: change.doc.id,
            ...change.doc.data(),
          });
        }
      });
      await nextTick();
      chatRef.value.scrollTo(0, chatRef.value.scrollHeight);
    });
    onCleanup(unsubscribe);
  }
});
</script>

<template>
  <q-page v-if="!userGoogle">
    <h3 class="text-center text-primary text-weight-bold">
      debes iniciar sesión :)
    </h3>
    <div class="row justify-center">
      <q-avatar size="106px" class="q-mb-sm">
        <img src="https://cdn.quasar.dev/img/boy-avatar.png" />
      </q-avatar>
    </div>
    <h5 class="text-center text-primary text-weight-bold">
      practica desarrollada por @gabrielserra.dev
    </h5>

    <div class="row justify-center">
      <div class="col-12 col-md-3">
        <q-card class="my-card">
          <q-item>
            <q-item-section avatar>
              <q-avatar>
                <img src="https://cdn.quasar.dev/img/boy-avatar.png" />
              </q-avatar>
            </q-item-section>

            <q-item-section>
              <q-item-label>Desarrollador en proceso</q-item-label>
              <q-item-label caption>practicando con quasar</q-item-label>
            </q-item-section>
          </q-item>

          <img src="https://cdn.quasar.dev/img/parallax2.jpg" />
        </q-card>
      </div>
    </div>
  </q-page>

  <q-page v-else padding>
    <div class="q-pa-md row justify-center scrollChat" ref="chatRef">
      <div style="width: 100%; max-width: 400px">
        <template v-for="message in messages" :key="message.id">
          <q-chat-message
            :text="[message.text]"
            :sent="message.uid === auth.currentUser.uid"
            :name="message.displayName"
          />
        </template>
      </div>
    </div>
  </q-page>
</template>

<style>
.scrollChat {
  height: calc(100vh - (60px + 60px));
  overflow-y: scroll;
}
</style>
