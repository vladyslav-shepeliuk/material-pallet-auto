<template>
  <div class="app" :style="{ backgroundColor: lightScheme.background }">
    <h1 :style="{ color: lightScheme.onBackground }">Form with Full Material 3 Palette</h1>

    <div class="color-picker">
      <label :style="{ color: lightScheme.onBackground }">
        Pick a seed color:
        <input type="color" v-model="seedColor" />
      </label>
    </div>

    <div class="card" :style="{ backgroundColor: lightScheme.background, color: lightScheme.onBackground }">
      <div class="card-content" :style="{ backgroundColor: lightScheme.surfaceVariant, color: lightScheme.onSurfaceVariant }">
        <h2 :style="{ color: lightScheme.primary }" class="card-title">Card Title</h2>
        <h3 :style="{ color: lightScheme.secondary }" class="card-subtitle">Card Subtitle</h3>
        <p class="card-text">Hello</p>
      </div>

      <div class="card-actions" :style="{ backgroundColor: lightScheme.surface }">
        <button
          class="primary-button"
          @mouseover="hoverPrimary = true"
          @mouseleave="hoverPrimary = false"
          :style="{
            backgroundColor: hoverPrimary ? lightScheme.inversePrimary : lightScheme.primary,
            color: lightScheme.onPrimary
          }"
        >
          Primary Action
        </button>
        <button
          class="secondary-button"
          @mouseover="hoverSecondary = true"
          @mouseleave="hoverSecondary = false"
          :style="{
            backgroundColor: hoverSecondary ? lightScheme.secondaryContainer : lightScheme.secondary,
            color: hoverSecondary ? lightScheme.onSecondaryContainer : lightScheme.onSecondary
          }"
        >
          Secondary Action
        </button>
      </div>
    </div>

    <div class="palettes">
      <div v-for="(color, name) in lightScheme" :key="name" class="color-box">
        <div class="swatch" :style="{ backgroundColor: color }"></div>
        <div class="label">{{ name }}<br>{{ color }}</div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch } from 'vue';
import { argbFromHex, hexFromArgb, themeFromSourceColor } from '@material/material-color-utilities';

export default defineComponent({
  setup() {
    const seedColor = ref('#6200EE');
    const lightScheme = ref<Record<string, string>>({});

    const hoverPrimary = ref(false);
    const hoverSecondary = ref(false);

    function generatePalette(seed: string) {
      const theme = themeFromSourceColor(argbFromHex(seed));
      const light = theme.schemes.light;
      return Object.fromEntries(
        Object.entries(light.toJSON()).map(([k, v]) => [k, hexFromArgb(v)])
      );
    }

    lightScheme.value = generatePalette(seedColor.value);

    watch(seedColor, (newColor) => {
      lightScheme.value = generatePalette(newColor);
    });

    return { seedColor, lightScheme, hoverPrimary, hoverSecondary };
  },
});
</script>

<style>
.app {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  min-height: 100vh;
  padding: 2rem;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.color-picker {
  margin: 1rem 0 2rem;
}

input[type="color"] {
  margin-left: 1rem;
  width: 3rem;
  height: 2rem;
  border: none;
  cursor: pointer;
}

.card {
  border-radius: 16px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.1);
  overflow: hidden;
  width: 400px;
  margin-bottom: 2rem;
}

.card-content {
  padding: 16px;
}

.card-title {
  margin: 0;
}

.card-subtitle {
  margin: 4px 0 12px;
}

.card-text {
  margin: 0;
}

.card-actions {
  display: flex;
  gap: 12px;
  padding: 16px;
}

.primary-button,
.secondary-button {
  border: none;
  padding: 8px 16px;
  border-radius: 8px;
  cursor: pointer;
}

.palettes {
  display: flex;
  flex-wrap: wrap;
  margin-top: 2rem;
  justify-content: center;
}

.color-box {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 0.5rem;
  width: 120px;
}

.swatch {
  width: 100px;
  height: 100px;
  border-radius: 12px;
  border: 1px solid #ccc;
}

.label {
  text-align: center;
  font-size: 0.8rem;
  margin-top: 0.5rem;
  word-break: break-word;
}
</style>
