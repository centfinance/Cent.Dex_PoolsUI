<template>
  <div class="p-4 pb-0">
    <p v-text="$('weightConstraints')" class="mb-4" />
    <UiTable>
      <UiTableTh>
        <div v-text="$t('tokens')" class="flex-auto text-left" />
        <div v-text="$t('weightsPct')" class="column" />
        <div v-text="$t('weights')" class="column-sm" />
      </UiTableTh>
      <UiTableTr v-for="(token, i) in tokens" :key="token">
        <Token :address="token" size="28" class="mr-2" />
        <div class="flex-auto text-left">
          {{ _ticker(token) }}
        </div>
        <div
          class="column text-right"
          v-if="weightRatio * startWeights[i]"
          v-text="
            _num(((weightRatio * startWeights[i]) / 100).toFixed(4), 'percent')
          "
        />
        <div class="column-sm text-right">
          <input
            v-model="startWeights[i]"
            type="number"
            class="input text-right ml-4"
            placeholder="0.0"
            maxlength="2"
            min="2"
            max="98"
            @input="$emit('input', startWeights)"
          />
        </div>
      </UiTableTr>
    </UiTable>
  </div>
</template>

<script>
export default {
  props: ['value', 'tokens'],
  data() {
    return {
      startWeights: []
    };
  },
  computed: {
    weightRatio() {
      return 100 / this.startWeights.reduce((a, b) => a + parseFloat(b), 0);
    }
  },
  created() {
    if (this.value.length === this.tokens.length)
      return (this.startWeights = this.value);
    this.tokens.forEach(() =>
      this.startWeights.push((100 / this.tokens.length).toFixed())
    );
    this.$emit('input', this.startWeights);
  }
};
</script>
