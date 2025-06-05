<script setup>

  import {doAjaxRequest} from "@/api";
  import { ref } from "vue";

  const listCategories = ref();

  function loadCategories() {
    doAjaxRequest("/api/categories?sort=code,desc")
        .then((dataJSON) =>
        {
          listCategories.value = dataJSON._embedded.categories;
        })
        .catch(showError);
  }

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
      <select v-for="(categorie, index) in listCategories" :key="categorie.code">
        <option>{{ categorie.libelle }}</option>
      </select>
      <!--
      <table>
        <tr>
          <th>Nom</th>
          <th>Prix</th>
          <th>Stock</th>
          <th>Commandés</th>
        </tr>
        <tr v-if="listProduits.length === 0">
          <td colspan="4">Veuillez patienter, chargement des catégories...</td>
        </tr>
        <tr v-for="produit in listProduits" :key="produit.reference">
          <td>{{ produit.nom }}</td>
          <td>{{ produit.prixUnitaire }}</td>
          <td>{{ produit.unitesEnStock }}</td>
          <td>{{ produit.unitesCommandees }}</td>
        </tr>
      </table>-->
    </div>
  </main>
</template>