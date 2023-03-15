<template>
    <div class="main">
      <h1 class="main-title">Recettes</h1>
  
      <form class="new-recipe-form" @submit.prevent="addRecipe">
        <input class="recipe-title-input" type="text" v-model="newRecipeTitle" placeholder="Titre de la recette" />
        <button class="recipe-create-button" type="submit">Ajouter</button>
      </form>
  
      <div class="recipe-book">
        <div class="recipe" v-for="(recipe, index) in recipes" :key="index">
          <div class="recipe-title">{{ recipe.title }}</div>
          <div class="recipe-actions">
            <button class="recipe-edit-button" @click="editRecipe(index)">Editer</button>
            <button class="recipe-delete-button" @click="deleteRecipe(index)">Supprimer</button>
            <input class="recipe-checkbox" type="checkbox" :id="'recipe-' + index" @click="addToShoppingList(recipe.ingredients)" />
            <label :for="'recipe-' + index">Ajouter à la liste de courses</label>
          </div>
        </div>
      </div>

  
      <div class="recipe-edit-form" v-if="editingRecipe !== null">
        <h1 class="main-title" style="text-align: center;">Livre de Recette</h1>

        <form @submit.prevent="saveRecipe">
          <input class="recipe-edit-title-input" type="text" v-model="editingRecipe.title" placeholder="Titre de la recette" />
          <textarea class="recipe-edit-description-input" v-model="editingRecipe.description" placeholder="Description"></textarea>
          <div class="recipe-edit-ingredients">
            <div class="ingredient" v-for="(ingredient, index) in editingRecipe.ingredients" :key="index">
              <input class="ingredient-name" type="text" v-model="ingredient.name" placeholder="Nom de l'ingrédient" />
              <input class="ingredient-quantity" type="text" v-model="ingredient.quantity" placeholder="Quantité" />
              <button class="ingredient-add-button" @click="addIngredient">Ajouter ingrédient</button>
            </div><button class="ingredient-add-button" @click="addIngredient">Ajouter ingrédient</button>

          </div>
          <div class="recipe-edit-buttons">
            <button class="recipe-save-button" type="submit">Enregistrer</button>
            <button class="recipe-cancel-button" @click="cancelEdit">Annuler</button>
          </div>
        </form>
      </div>

      <div class="shopping-list" v-if="shoppingList.length > 0">
        <h1 class="main-title" style="text-align: center;">Liste de course</h1>

        <h2>Liste de courses</h2>
        <ul>
          <li v-for="(item, index) in shoppingList" :key="index">{{ item.name }} - {{ item.quantity }} - {{ item.description }}</li>
        </ul>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        newRecipeTitle: "",
        editingRecipe: null,
        recipes: [],
        shoppingList: [],
        newIngredient: {
          name: "",
          quantity: "",
        },
      };
    },
    methods: {
        addRecipe() {
            if (this.newRecipeTitle) {
                this.recipes.push({
                    title: this.newRecipeTitle,
                    description: "",
                    ingredients: [],
                });
                this.newRecipeTitle = "";
      }
    },
        editRecipe(index) {
            this.editingRecipe = Object.assign({}, this.recipes[index]);
        },
        saveRecipe() {
            if (this.editingRecipe) {
                const index = this.recipes.findIndex(
                    (recipe) => recipe.title === this.editingRecipe.title);
            if (index >= 0) {
                this.recipes.splice(index, 1, this.editingRecipe);
                this.editingRecipe = null;
            }
        }
        },
        cancelEdit() {
            this.editingRecipe = null;
        },
        deleteRecipe(index) {
            this.recipes.splice(index, 1);
        },
        deleteRecipeByTitle(title) {
            const index = this.recipes.findIndex((recipe) => recipe.title === title);
            if (index >= 0) {
                this.recipes.splice(index, 1);
                this.editingRecipe = null;
            }
        },
        addIngredient() {
            this.editingRecipe.ingredients.push({ ...this.newIngredient });
            this.newIngredient.name = "";
            this.newIngredient.quantity = "";
        },
        addToShoppingList(ingredients) {
            for (let i = 0; i < ingredients.length; i++) {
                const ingredient = ingredients[i];
                const existingItemIndex = this.shoppingList.findIndex((item) => item.name === ingredient.name);
                if (existingItemIndex !== -1) {
                    this.shoppingList[existingItemIndex].quantity += ingredient.quantity;
                } else {
                    this.shoppingList.push({ name: ingredient.name, quantity: ingredient.quantity });
                } 
            }
        }
    },
    };
</script>

<style>
body {
  margin: 0;
  display: flex;
  place-items: center;
  min-width: 320px;
  min-height: 100vh;
  background-color: #ebf4ff;
}

button {
  border-radius: 8px;
  border: 1px solid transparent;
  padding: 0.6rem 1.2rem;
  font-size: 1rem;
  font-weight: 500;
  font-family: inherit;
  background-color: #1e3041;
  color: #fff;
  cursor: pointer;
  transition: all 0.25s;
  outline: none;
}
button:hover {
  background-color: #45627d;
}
button:focus-visible {
  box-shadow: inset 0 0 0 2px #45627d, inset 0 0 0 4px #6589ab;
}

input[type="checkbox"] {
  appearance: none;
  background-color: transparent;
  border: 1px solid #1e3041;
  border-radius: 4px;
  width: 1.2rem;
  height: 1.2rem;
  cursor: pointer;
  transition: all 0.25s;
}
input[type="checkbox"]:checked {
  background-color: #1e3041;
}

.main {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100vw;
  min-height: 100vh;
  overflow-y: auto;
  color: #1e3041;
  font-family: 'Roboto', -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Oxygen',
    'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue',
    sans-serif;
}

.main-title {
  font-size: 3rem;
  font-weight: bold;
  margin: 0 0 2rem;
  text-shadow: 0 -1px 1px #415c71;
}

.new-recipe-form {
  display: flex;
  align-items: stretch;
  gap: 0.5rem;
  border-radius: 0.5rem;
  background-color: #fff;
  box-shadow: 0 0 0.5rem rgba(0, 0, 0, 0.1);
  margin-bottom: 2rem;
}

.recipe-title-input {
  font-size: 1.5rem;
  font-weight: 500;
  border: none;
  outline: none;
  background-color: transparent;
  padding: 1rem;
  color: #1e3041;
}

.recipe-create-button {
  outline: none;
  border: none;
  font-size: 1.5rem;
  background-color: #1e3041;
  color: #fff;
  transition: background-color 0.2s ease-in-out;
}

.recipe-create-button:hover {
  background-color: #415c71;
}

.recipe-book {
  display: flex;
  flex-direction: column;
  align-items: stretch;
  min-width: 400px;
  max-width: 800px;
  width: 100%;
  border-radius: 0.5rem;
  background-color: #fff;
  box-shadow: 0 0 0.5rem rgba(0, 0, 0, 0.1);
  margin-bottom: 2rem;
}

.recipe {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 0.5rem;
  padding: 1rem;
}

.recipe:not(:last-child) {
  border-bottom: 1px solid #ececec;
}

.recipe-title {
  font-size: 1.5rem;
  font-weight: 500;
}

.recipe-actions {
  display: flex;
  gap: 0.5rem;
}

.recipe-edit-button {
  outline: none;
  border-color: #1e3041;
  background-color: transparent;
  color: #1e3041;
  transition: all 0.2s ease-in-out;
}
.recipe-edit-button:hover {
  border-color: #45627d;
  background-color: transparent;
  color: #45627d;
}

.recipe-delete-button {
  outline: none;
  border: none;
  transition: all 0.2s ease-in-out;
}

.recipe-edit-form {
  border-radius: 0.5rem;
  background-color: #fff;
  box-shadow: 0 0 0.5rem rgba(0, 0, 0, 0.1);
  margin-bottom: 2rem;
  min-width: 400px;
  max-width: 800px;
  width: 100%;
}

.recipe-edit-title-input {
  display: block;
  font-size: 1.5rem;
  font-weight: 500;
  border: none;
  outline: none;
  background-color: transparent;
  padding: 1rem;
  color: #1e3041;
  width: 100%;
  box-sizing: border-box;
}

.recipe-edit-description-input {
  display: block;
  font-size: 1rem;
  font-weight: 500;
  border: none;
  outline: none;
  background-color: transparent;
  padding: 1rem;
  color: #1e3041;
  width: 100%;
  box-sizing: border-box;
}

.recipe-ingredients-title {
  font-size: 1.2rem;
  font-weight: 800;
  margin: 0;
  padding: 1rem;
}

.recipe-ingredients-list {
  display: flex;
  flex-direction: column;
  align-items: stretch;
  gap: 0.5rem;
  padding: 0 1rem 1rem;
  margin: 0;
}

.recipe-ingredient, .recipe-new-ingredient {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 0.5rem;
  padding: 0.5rem;
  border-radius: 0.5rem;
  background-color: #ececec;
}

.recipe-ingredient-input, .recipe-new-ingredient-input {
  flex: 1;
  font-size: 1rem;
  font-weight: 500;
  border: none;
  outline: none;
  background-color: transparent;
  padding: 0.5rem;
  color: #1e3041;
}

.recipe-new-ingredient {
  align-items: stretch;
  padding: 0;
}

.recipe-new-ingredient-input {
  padding: 1rem;
}

.recipe-edit-save-button, .recipe-edit-cart-button {
  flex: 1;
}

.recipe-ingredient-delete-button {
  opacity: 0;
  outline: none;
  border-color: #1e3041;
  background-color: transparent;
  color: #1e3041;
  transition: all 0.2s ease-in-out;
  padding: 0.3rem 0.5rem;
}
.recipe-ingredient-delete-button:hover {
  border-color: #45627d;
  background-color: transparent;
  color: #45627d;
}
.recipe-ingredient:hover .recipe-ingredient-delete-button {
  opacity: 1;
}

.recipe-edit-actions {
  display: flex;
  gap: 0.5rem;
  padding: 1rem;
}

.shopping-list {
  display: flex;
  flex-direction: column;
  align-items: stretch;
  min-width: 400px;
  max-width: 800px;
  width: 100%;
  border-radius: 0.5rem;
  background-color: #fff;
  box-shadow: 0 0 0.5rem rgba(0, 0, 0, 0.1);
  margin-bottom: 2rem;
}

.shopping-list-title {
  font-size: 1.2rem;
  font-weight: 800;
  margin: 0;
  padding: 1rem;
}

.shopping-list-item {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  flex: 1;
  cursor: pointer;
}

.shopping-list-actions {
  display: flex;
  gap: 0.5rem;
  padding: 1rem;
}

  </style>