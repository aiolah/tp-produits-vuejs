<script setup>

  import {doAjaxRequest} from "@/api";
  import { reactive, onMounted, ref } from "vue";

  const listProduits = ref([]);
  const baseUrl = ref("/api/produits?page=0&size=5");
  const currentPage = ref(1);
  const nbPages = ref();
  const nextUrl = ref('');
  const prevUrl = ref('');
  const lastUrl = ref('');
  const firstUrl = ref('');
  const lastPageDisabled = ref(false);
  const firstPageDisabled = ref(false);

  function loadProduits(url) {
    doAjaxRequest(url)
        .then((dataJSON) =>
        {
          listProduits.value = dataJSON._embedded.produits;
          nbPages.value = dataJSON.page.totalPages;
          firstUrl.value = dataJSON._links.first.href;
          lastUrl.value = dataJSON._links.last.href;
          currentPage.value = dataJSON.page.number;

          if(dataJSON._links.prev)
          {
            prevUrl.value = dataJSON._links.prev.href;
          }

          if(dataJSON._links.next)
          {
            nextUrl.value = dataJSON._links.next.href;
          }
        })
        .catch(showError);
  }

  function showError(error) {
    console.log("Erreur : status %d", error.status)
    console.log(error.body);
    alert(error.message);
  }

  function nextPage()
  {
    let nextPage = currentPage.value + 1;
    if(nextPage < nbPages.value)
    {
      loadProduits(nextUrl.value);
      currentPage.value++;

      // La prochaine page est la dernière
      if(nextPage == nbPages.value - 1)
      {
        lastPageDisabled.value = true;
        firstPageDisabled.value = false;
      }
      else
      {
        lastPageDisabled.value = false;
        firstPageDisabled.value = false;
      }
    }
  }

  function lastPage()
  {
    loadProduits(lastUrl.value);
    lastPageDisabled.value = true;
    firstPageDisabled.value = false;
  }

  function previousPage()
  {
    if(currentPage.value - 1 >= 0)
    {
      loadProduits(prevUrl.value);
      currentPage.value--;

      // La précédente page est la première
      if(currentPage.value == 0)
      {
        firstPageDisabled.value = true;
        lastPageDisabled.value = false;
      }
      else
      {
        firstPageDisabled.value = false;
        lastPageDisabled.value = false;
      }
    }
  }

  function firstPage()
  {
    loadProduits(firstUrl.value);
    firstPageDisabled.value = true;
    lastPageDisabled.value = false;
  }

  onMounted(() => {
    loadProduits(baseUrl.value);
  });

</script>

<template>
  <main>
    <div>
      <h1>Les produits</h1>
      <!-- Un formulaire pour saisir les valeurs de la catégorie à ajouter -->
      <!--<form @submit.prevent="ajouteCategorie">
        <div>
          <input id="libelle" v-model="" placeholder="Libelle" />
        </div>
        <div>
          <input id="description" v-model="" placeholder="Description" />
        </div>
        <button type="submit">Ajouter</button>
      </form>-->
    </div>
    <div>
      <table>
        <caption>Les produits - Page {{ currentPage + 1 }}/{{ nbPages }}</caption>
        <tr>
          <th>Nom</th>
          <th>Prix</th>
          <th>Stock</th>
          <th>Commandés</th>
        </tr>
        <!-- Si le tableau des catégories est vide -->
        <tr v-if="listProduits.length === 0">
          <td colspan="4">Veuillez patienter, chargement des catégories...</td>
        </tr>
        <!-- Si le tableau des catégories n'est pas vide -->
        <tr v-for="produit in listProduits" :key="produit.reference">
          <td>{{ produit.nom }}</td>
          <td>{{ produit.prixUnitaire }}</td>
          <td>{{ produit.unitesEnStock }}</td>
          <td>{{ produit.unitesCommandees }}</td>
        </tr>
      </table>
      <table>
        <tr>
          <td class="arrow">
            <button @click="firstPage" :disabled="firstPageDisabled">⇇</button>
          </td>
          <td class="arrow">
            <button @click="previousPage" :disabled="firstPageDisabled">←</button>
          </td>
          <td class="arrow">
            <button @click="nextPage" :disabled="lastPageDisabled">→</button>
          </td>
          <td class="arrow">
            <button @click="lastPage" :disabled="lastPageDisabled">⇉</button>
          </td>
        </tr>
      </table>
    </div>
  </main>
</template>

<style scoped>
td,
th {
  border: 1px solid #ddd;
  padding: 8px;
}

th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #232623;
  color: rgb(255, 255, 255);
}

.arrow {
  width: 107px;
}
</style>