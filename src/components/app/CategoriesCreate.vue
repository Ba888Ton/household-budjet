<template>
  <div class="col s12 m6">
    <div>
      <div class="page-subtitle">
        <h4>Создать</h4>
      </div>
      <form @submit.prevent="submitHandler">
        <div class="input-field">
          <input 
            id="name" 
            type="text" 
            v-model="name"
            :class="{invalid: $v.name.$dirty && !$v.name.required}"
          />
          <label for="name">Название</label>
          <span 
            v-if="$v.name.$dirty && !$v.name.required" 
            class="helper-text invalid"
          >
            Введите название категории
          </span>
        </div>
        <div class="input-field">
          <input 
            id="limit" 
            type="number"
            v-model.number="limit"
            :class="{invalid: $v.limit.$dirty && !$v.limit.minValue}"
          />
          <label for="limit">Лимит</label>
          <span 
            class="helper-text invalid"
            v-if="$v.limit.$dirty && !$v.limit.minValue"
          >
            Минимальная величина 
          </span>
        </div>
        <button class="btn waves-effect waves-light" type="submit">
          Создать
          <i class="material-icons right">send</i>
        </button>
      </form>
    </div>
  </div>
</template>

<script>
import {required, minValue} from 'vuelidate/lib/validators'

export default {
  name: 'CategoriesCreate',
  data: () => ({
      name: '',
      limit: 100,
    })
  ,
  validations: {
    name: { required },
    limit: { minValue: minValue(1) },
  },
  mounted () {
    window.M.updateTextFields();
  },
  methods: {
    async submitHandler() {
      if (this.$v.$invalid) {
        console.log('submit handler');
        this.$v.$touch()
        return
      }
      try {
        const category = await this.$store.dispatch('createCategory', {
          name: this.name,
          limit: this.limit
        })
        this.name = '',
        this.limit = 100,
        this.$v.$reset()
        this.$message(`Категория "${category.name}" была создана`)
        this.$emit('created', category.name)
      } catch (e) {
        console.log(e);
      }
    }
  },
};
</script>

<style lang="scss" scoped>
</style>
