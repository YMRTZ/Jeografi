<template>
  <q-layout view="lHh Lpr lFf">
    <q-page-container>
      <div>
        <div class="row">
          <div class="col-2 q-mx-md">
            <div class="row">
              <q-input v-model="name" label="Name" />
            </div>
            <div class="row q-mt-md">
              <select v-model="type">
                <option value="exists">Exists</option>
                <option value="owns">Owns</option>
                <option value="named">Named</option>
              </select>
            </div>
            <div class="row">
              <q-input
                v-model="name2"
                label="Name 2"
                v-if="type == 'owns' || type == 'named'"
              />
            </div>
            <div class="row">
              <q-input v-model="startYear" label="Start Year" />
            </div>
            <div class="row">
              <q-input v-model="endYear" label="End Year" />
            </div>
            <div class="row q-mt-md">
              <select v-model="region">
                <option value="na">North America</option>
                <option value="la">Latin America</option>
                <option value="me">Middle East</option>
                <option value="we">Western Europe</option>
                <option value="ee">Eastern Europe</option>
                <option value="bk">Balkans</option>
                <option value="af">Subsaharan Africa</option>
                <option value="si">Siberia</option>
                <option value="ca">Central Asia</option>
                <option value="sa">South Asia</option>
                <option value="ea">East Asia</option>
                <option value="se">Southeast Asia</option>
              </select>
            </div>
            <div class="q-mt-md">
              <q-btn @click="writeEvent()">Save</q-btn>
            </div>
            <div class="q-mt-md">
              <q-btn @click="loadEvent()">Load</q-btn>
            </div>
          </div>
          <div class="col">
            <div class="row">
              <div class="col-3 bordered q-pl-sm"><b>Name</b></div>
              <div class="col-1 bordered q-pl-sm"><b>Type</b></div>
              <div class="col-2 bordered q-pl-sm"><b>Name 2</b></div>
              <div class="col-1 bordered q-pl-sm"><b>Start Year</b></div>
              <div class="col-1 bordered q-pl-sm"><b>End Year</b></div>
              <div class="col-1 bordered q-pl-sm"><b>Region</b></div>
              <div class="col-2 bordered q-pl-sm"><b>ID</b></div>
              <div class="col-1 bordered q-pl-sm"><b>Delete</b></div>
            </div>
            <div class="row" v-for="event in eventCollection" :key="event.id">
              <div class="col-3 bordered q-pl-sm">{{ event.data().name }}</div>
              <div class="col-1 bordered q-pl-sm">{{ event.data().type }}</div>
              <div class="col-2 bordered q-pl-sm">{{ event.data().name2 }}</div>
              <div class="col-1 bordered q-pl-sm">
                {{ event.data().startYear }}
              </div>
              <div class="col-1 bordered q-pl-sm">
                {{ event.data().endYear }}
              </div>
              <div class="col-1 bordered q-pl-sm">
                {{ event.data().region }}
              </div>
              <div class="col-2 bordered q-pl-sm">{{ event.id }}</div>
              <div class="col-1 bordered q-pl-sm">
                <button @click="deleteEvent(event.id)">Delete</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </q-page-container>
  </q-layout>
</template>

<style lang="scss">
.bordered {
  border: 1px solid black;
}
</style>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import {
  collection,
  addDoc,
  getDocs,
  getFirestore,
  DocumentData,
  deleteDoc,
  doc,
} from 'firebase/firestore';

export default defineComponent({
  name: 'MainLayout',

  setup() {
    // eslint-disable-next-line @typescript-eslint/no-explicit-any
    function print(input: any) {
      console.log(input);
    }

    const fireStore = getFirestore();

    let name = ref('');
    let name2 = ref('');
    let type = ref('');
    let startYear = ref(-5000);
    let endYear = ref(9999);
    // 9999 is code for present
    let region = ref('');

    async function writeEvent() {
      void (await addDoc(collection(fireStore, 'events'), {
        name: name.value,
        type: type.value,
        name2: name2.value,
        startYear: startYear.value,
        endYear: endYear.value,
        region: region.value,
      }));

      name.value = '';
      type.value = '';
      name2.value = '';
      startYear.value = -5000;
      endYear.value = 9999;
      region.value = '';

      loadEvent();
    }

    async function deleteEvent(id: string) {
      void (await deleteDoc(doc(fireStore, 'events', id)));
      loadEvent();
    }

    let eventCollection = ref<DocumentData[]>([]);

    async function loadEvent() {
      eventCollection.value = [];
      var querySnapshot = await getDocs(collection(fireStore, 'events'));
      querySnapshot.forEach((doc) => {
        eventCollection.value.push(doc);
        console.log(doc.data());
      });
      console.log(eventCollection);
    }

    loadEvent();
    return {
      fireStore,
      print,
      writeEvent,
      loadEvent,
      deleteEvent,
      name,
      name2,
      type,
      startYear,
      endYear,
      region,
      eventCollection,
    };
  },
});
</script>
