<template>
  <ion-menu content-id="main-content">
    <ion-header>
      <ion-toolbar>
        <ion-title>Menu Content</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content class="ion-padding">
      Additional filters:
      <br> Store: <br>
      <ion-item v-for="store in chains" :key="store.id">
        <ion-checkbox label-placement="start" @ionChange="handleCheckboxChange($event)">{{ store.name }}</ion-checkbox>
      </ion-item>
    </ion-content>
  </ion-menu>
  <ion-page id="main-content">
    <ion-header>
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-menu-button></ion-menu-button>
        </ion-buttons>
        <ion-title>Euro-pincher</ion-title>
        <ion-searchbar show-cancel-button="never" placeholder="Type to find a product"
                       @ionInput="handleChange($event)"></ion-searchbar>
      </ion-toolbar>
    </ion-header>
    <ion-content class="ion-padding">
      <ion-grid>
        <ion-row>
          <ion-col size="6" size-md="4" size-lg="2" v-for="result in results" :key="result.id">
            <ion-card>
              <img :alt=result.name :src=result.image height="300" width="300"/>
              <ion-card-header>
                <ion-card-title>{{ result.name }}</ion-card-title>
                <ion-card-subtitle> {{ result.description }}</ion-card-subtitle>
              </ion-card-header>
              <ion-card-content>
                Price: {{ result.price }} Discount <b> {{ result.discount }}.</b><br>
                Store: <b>{{ result.store }}</b>
              </ion-card-content>
            </ion-card>
          </ion-col>
        </ion-row>
      </ion-grid>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import {IonCard, IonSearchbar, IonCheckbox, IonPage} from '@ionic/vue';
import {defineComponent, ref} from 'vue';

export default defineComponent({
  components: {IonCard, IonSearchbar, IonCheckbox, IonPage},
  setup() {
    const results = ref([]);
    const chains = ref([]);
    const fetchProducts = async (query = '') => {
      try {
        const response = await fetch(`http://localhost:7999/prices/search?name=${encodeURIComponent(query)}`);
        const data = await response.json();
        results.value = data.content;
      } catch (error) {
        console.error(error);
      }
    };
    const fetchStores = async () => {
      try {
        const response = await fetch(`http://localhost:7999/stores`);
        const data = await response.json();
        chains.value = data;
      } catch (error) {
        console.error(error);
      }
    };
    const handleChange = (event: any) => {
      const query = event.target.value.toLowerCase();
      fetchProducts(query);
      fetchStores();
    };

    const handleCheckboxChange = () => {
      console.log('checked')
    };
    return {results, chains, handleChange, handleCheckboxChange};
  }
});

</script>

<style scoped>
#container {
  text-align: center;

  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;

  color: #8c8c8c;

  margin: 0;
}

#container a {
  text-decoration: none;
}
</style>
