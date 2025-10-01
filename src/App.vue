<template>
  <div class="app" :style="{ backgroundColor: lightScheme.background }">
    <h1 :style="{ color: lightScheme.onBackground }">Form with Full Material 3 Palette</h1>

    <div class="color-picker">
      <label :style="{ color: lightScheme.onBackground }">
        Pick a seed color:
        <input type="color" v-model="seedColor" />
      </label>
    </div>

    <form class="demo-form" @submit.prevent="submitForm"
          :style="{ backgroundColor: lightScheme.surface, color: lightScheme.onSurface }">

      <label>
        Name:
        <input type="text"
               v-model="form.name"
               :style="inputStyle(errors.name)" placeholder="Enter your name" />
        <span v-if="errors.name" :style="errorStyle">{{ errors.name }}</span>
      </label>

      <label>
        Email:
        <input type="email"
               v-model="form.email"
               :style="inputStyle(errors.email)" placeholder="Enter your email" />
        <span v-if="errors.email" :style="errorStyle">{{ errors.email }}</span>
      </label>

      <label>
        Password:
        <input type="password"
               v-model="form.password"
               :style="inputStyle(errors.password)" placeholder="Password" />
        <span v-if="errors.password" :style="errorStyle">{{ errors.password }}</span>
      </label>

      <div class="buttons">
        <button type="submit" :style="{ backgroundColor: lightScheme.primary, color: lightScheme.onPrimary }">Submit</button>
        <button type="button" @click="resetForm" :style="{ backgroundColor: lightScheme.secondary, color: lightScheme.onSecondary }">Reset</button>
      </div>
    </form>

    <div class="palettes">
      <div v-for="(color, name) in lightScheme" :key="name" class="color-box">
        <div class="swatch" :style="{ backgroundColor: color }"></div>
        <div class="label">{{ name }}<br>{{ color }}</div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, reactive, watch } from 'vue';
import { argbFromHex, hexFromArgb, themeFromSourceColor } from '@material/material-color-utilities';

export default defineComponent({
  setup() {
    const seedColor = ref('#6200EE');
    const lightScheme = ref<Record<string, string>>({});

    const form = reactive({ name: '', email: '', password: '' });
    const errors = reactive({ name: '', email: '', password: '' });

    function generatePalette(seed: string) {
      const theme = themeFromSourceColor(argbFromHex(seed));
      const light = theme.schemes.light;
      // Собираем все нужные цвета
      return {
        ...Object.fromEntries(Object.entries(light.toJSON()).map(([k, v]) => [k, hexFromArgb(v)])),
        errorContainer: hexFromArgb(light.errorContainer),
        onErrorContainer: hexFromArgb(light.onErrorContainer),
        tertiaryContainer: hexFromArgb(light.tertiaryContainer),
        onTertiaryContainer: hexFromArgb(light.onTertiaryContainer),
      };
    }

    lightScheme.value = generatePalette(seedColor.value);
    watch(seedColor, (newColor) => {
      lightScheme.value = generatePalette(newColor);
    });

    function submitForm() {
      errors.name = form.name ? '' : 'Name is required';
      errors.email = form.email ? '' : 'Email is required';
      errors.password = form.password ? '' : 'Password is required';
      if (!errors.name && !errors.email && !errors.password) alert('Form submitted!');
    }

    function resetForm() {
      form.name = ''; form.email = ''; form.password = '';
      errors.name = ''; errors.email = ''; errors.password = '';
    }

    function inputStyle(hasError: string) {
      return {
        backgroundColor: lightScheme.value.surfaceVariant,
        color: lightScheme.value.onSurfaceVariant,
        borderColor: hasError ? lightScheme.value.error : lightScheme.value.outline,
        borderWidth: '2px'
      };
    }

    const errorStyle = {
      color: lightScheme.value.onErrorContainer || lightScheme.value.error,
      backgroundColor: lightScheme.value.errorContainer,
      padding: '0.2rem 0.4rem',
      borderRadius: '4px',
      fontSize: '0.8rem',
      marginTop: '0.2rem'
    };

    return { seedColor, lightScheme, form, errors, submitForm, resetForm, inputStyle, errorStyle };
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

.demo-form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  padding: 2rem;
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.1);
  width: 100%;
  max-width: 400px;
}

.demo-form label {
  display: flex;
  flex-direction: column;
  font-size: 0.9rem;
}

.demo-form input {
  padding: 0.5rem;
  border-radius: 6px;
  margin-top: 0.3rem;
  transition: border-color 0.3s ease, background-color 0.3s ease;
}

.buttons {
  display: flex;
  justify-content: flex-end;
  gap: 1rem;
  margin-top: 1rem;
}

.demo-form button {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-weight: bold;
  transition: transform 0.1s ease;
}

.demo-form button:hover {
  transform: scale(1.05);
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
