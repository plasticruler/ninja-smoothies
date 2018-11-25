<template>
    <div class="add-smoothie container">
        <h2 class="center-align indigo-text">Add New Smoothie Recipe</h2>
        <form @submit.prevent="AddSmoothie">
            <div class="field title">
                <label for="title">Smoothie Title:</label>
                <input type="text" name="title" v-model="title">
            </div>           
            <div class="field input-field">
            <label for="textarea1">Method</label>
          <textarea id="textarea1" class="materialize-textarea"></textarea>
        </div>
            
            <div v-for="(ingredient, index) in ingredients" :key="index" class="field">
                <label for="ingredient">Ingredient:</label>
                <input type="text" name="ingredient" v-model="ingredients[index]">
                <i class="material-icons delete" @click="DeleteIngredient(ingredient)">delete</i>
            </div>          
            <div class="field add-ingredient">
                <label for="add-ingredient">Add ingredient:</label>
                <input type="text" name="add-ingredient" @keydown.tab.prevent = "AddIngredient" v-model="another">
            </div>
            <p v-if="feedback" class="red-text">{{feedback}}</p>
            <div class="field center-align">
                <button class="btn pink">Add Smoothie</button>
            </div>
        </form>
    </div>   
</template>
<script>
import db from "@/firebase/init";
import slugify from "slugify";
export default {
  name: "AddSmoothie",
  data() {
    return {
      title: null,
      another: null,
      ingredients: [],
      feedback: null,
      slug: null,
      added: null
    };
  },
  methods: {
    DeleteIngredient(ing) {
      this.ingredients = this.ingredients.filter(ingredient => {
        return ingredient != ing;
      });
    },
    AddIngredient() {
      if (this.another) {
        this.ingredients.push(this.another);
        this.another = null;
        this.feedback = null;
        //let i = this.ingredients.split('capital cap')
      } else {
        this.feedback = "You must enter a value to add an ingredient.";
      }
    },
    AddSmoothie() {
      if (this.title) {
        this.feedback = null;
        //create slug
        this.slug = slugify(this.title, {
          replacement: "-",
          remove: /[$*_+~.()'"!\-:@']/g,
          lower: true
        });
        db.collection("smoothies")
          .add({
            title: this.title,
            ingredients: this.ingredients,
            slug: this.slug
          })
          .then(() => {
            this.$router.push({
              name: "Index"
            });
          })
          .catch(err => {
            console.error(err);
          });
      } else {
        this.feedback = "Please enter a title.";
      }
    }
  }
};
</script>
<style>
.add-smoothie {
  margin-top: 60px;
  padding: 20px;
  max-width: 500px;
}
.add-smoothie h2 {
  font-size: 2em;
  margin: 20px auto;
}
.add-smoothie .field {
  margin: 20px auto;
  position: relative;
}
.add-smoothie .delete {
  position: absolute;
  right: 0;
  bottom: 16px;
  color: #aaa;
  font-size: 1.4em;
  cursor: pointer;
}
</style>

