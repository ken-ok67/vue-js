<template>
  <BaseFormField :title="title" :error="error">
    <input class="form__input" v-model="dataValue" :type="type"
           name="name" :placeholder="placeholder">
  </BaseFormField>
</template>

<script>
import formFieldMixin from '@/mixins/fromFieldMixin';

export default {
  name: 'BaseFormText',
  props: ['type'],
  mixins: [formFieldMixin],
};
</script>
