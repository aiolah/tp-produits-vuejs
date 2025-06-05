<script setup>

  import {doAjaxRequest} from "@/api";
  import { ref, onMounted } from "vue";

  const listCategories = ref([]);
  const listProduits = ref([]);
  const categorieSelected = ref('');

  function loadCategories() {
    doAjaxRequest("/api/categories?sort=code,desc")
        .then((dataJSON) =>
        {
          listCategories.value = dataJSON._embedded.categories;
        })
        .catch(showError);
  }

  function loadProduitsPerCategories()
  {
    doAjaxRequest(`/api/categories/${categorieSelected.value}/produits`)
        .then((dataJSON) =>
        {
          listProduits.value = dataJSON._embedded.produits;
        })
        .catch(showError);
  }

  function showError(error) {
    console.log("Erreur : status %d", error.status)
    console.log(error.body);
    alert(error.message);
  }

  onMounted(() => {
    loadCategories();
  });

</script>

<template>
  <main>
    <div>
      <h1>Les produits</h1>
      <select @change="loadProduitsPerCategories" v-model="categorieSelected">
        <option disabled value="">--- Veuillez choisir une catégorie ---</option>
        <option v-for="(categorie, index) in listCategories" :key="categorie.code" :value="categorie.code">{{ categorie.libelle }}</option>
      </select>
      <table>
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