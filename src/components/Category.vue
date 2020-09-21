<template>
  <div class="container">
    <div class="category-wrapper">
      <div class="category-label">Categories</div>
      <div class="category-inputs-wrapper">
        <template>
          <div
            class="category-selected-wrapper"
            v-for="(cat, i) in selected"
            :key="i"
          >
            <div class="category-selected">{{cat.category.title}} / {{cat.subCategory.title}}</div>
            <button class="del" @click="del(cat)" v-if="selected.length > 1">x</button>
          </div>
        </template>
        <template v-if="!stopAdd">
          <div v-if="showButton">
            <button @click="showButton = !showButton">+ Add category</button>
          </div>
          <div class="category-inputs" v-else>
            <select name="" id="" v-model="form.category" @change="$set(form, 'subCategory', null)">
              <option :value="null" disabled>- Select category -</option>
              <option :value="cat" v-for="(cat, i) in categories" :key="i">{{cat.title}}</option>
            </select>
            <select name="" id="" v-model="form.subCategory" :disabled="!form.category" @change="add">
              <option :value="null" disabled>- Select subcategory -</option>
              <option :value="sub" v-for="(sub, i) in subCategories" :key="i">{{sub.title}}</option>
            </select>
            <button class="del" @click="showButton = !showButton" v-if="selected.length > 1">x</button>
          </div>
        </template>
      </div>
    </div>
  </div>
</template>
<script>
import categoriesRaw from '@/data/categories.json';

export default {
  data() {
    return {
      categories: {},
      form: {
        category: null,
        subCategory: null,
      },
      selected: [],
      showButton: false,
    }
  },
  computed: {
    subCategories() {
      let result = []
      const subCategories = this.categories.length && this.categories.filter((el) => el.id === this.form.category?.id)[0];
      result = subCategories ? subCategories.childs : [];
      
      if (this.selected.length > 0) {
        // filter by selected
        result = result.filter((el) => !this.selected.some((sel) => sel.subCategory.id === el.id));

        // filter by params === 0
        result = result.filter((el) => el.params === 0);
      } 


      return result;
    },
    stopAdd() {
      return !!this.selected[this.selected.length - 1]?.subCategory?.params;
    }
  },
  mounted() {
    this.categories = categoriesRaw;
    console.log(this.categories);
  },
  methods: {
    add() {
      this.selected.push({...this.form, id: `${Math.random()*10000}.${Date.now()}`});
      this.form = {
        category: null,
        subCategory: null,
      };
      this.showButton = true;
    },
    del(cat) {
      this.selected = this.selected.filter((el) => el.id !== cat.id);
    },
  },
}
</script>
<style lang="scss">
  $tablet: 480px;
  $primary: #3c83ca;

  @mixin media-breakpoint($min) {
    @media (min-width: $min) {
      @content;
    }
  }

  .container {
    padding: 0 1rem;
  }

  .category-wrapper {
    margin: 0 -1rem;
    background-color: #e6e9ec;
    display: flex;
    flex-direction: column;

    &>div {
      padding: 1rem;
    }


    button {
      padding: .5rem 1rem;
      cursor: pointer;
    }

    select {
      padding: .5rem .25rem;
    }

    .del {
      color: red;
      height: 2.25rem;
      width: 2.25rem;
      padding: .625rem;
      text-transform: uppercase;
      border: none;
      background: transparent;
    }
    
    .category-inputs-wrapper {
      flex-grow: 1;
      display: flex;
      flex-direction: column;

      .category-selected-wrapper {
        display: flex;
        .category-selected {
          padding: .5rem;
          border: 1px dashed grey;
          color: $primary;
          flex: 1;
        }

        &:not(:last-child) {
          margin-bottom: 1rem;
        }
      }

      .category-inputs {
        flex: 1;
        display: flex;

        select {
          flex: 1;
          &:not(:last-child) {
            margin-right: 1rem;
          }
        }
      }
    }
  }

  @include media-breakpoint($tablet) {
    .category-wrapper {
      flex-direction: row;
    }
  }
</style>
