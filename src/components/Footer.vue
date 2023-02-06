<script setup>
import { ref } from "vue";
import { collection, addDoc } from "firebase/firestore";
import { db, auth } from '../firebase'
import { useQuasar } from 'quasar'

const text = ref("");
const $q = useQuasar()

const addText = () => {
  if(text.value.trim() !== ''){
    addDoc(collection(db, "chats"), {
      text: text.value,
      uid: auth.currentUser.uid,
      time: Date.now(),
      displayName: auth.currentUser.displayName,
    })
    .then(() => {
      console.log("aqui iria un sonido")
      text.value = "";
    })
    .catch((e) => console.log(e));
  }else{
    $q.notify({
      message: 'No puedes enviar un mensaje vacío',
      color: 'negative',
      icon:'error'
    });
    text.value = '';
  }

}
</script>

<template>
  <q-footer elevated class="primary">
    <q-toolbar>
      <q-input
        class="full-width"
        dark dense standout
        label="Ingrese texto"
        v-model="text"
        @keyup.enter="addText"
      >
        <template v-slot:append>
          <q-icon name="send" class="cursor-pointer" type="submit" @click="addText" />
        </template>
      </q-input>
    </q-toolbar>
  </q-footer>
</template>
