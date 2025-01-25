<template>
  <div class="container py-5">
    <h1 class="mb-4">Configure Form via JSON</h1>

    <!-- JSON Input Options -->
    <div class="mb-4">
      <h2 class="fw-bold">Upload JSON File or Input JSON</h2>

      <!-- Form Title Input -->
      <div class="mb-3">
        <label for="form-title" class="form-label">Form Title</label>
        <input
            type="text"
            id="form-title"
            v-model="formTitle"
            class="form-control"
            placeholder="Enter form title"
        />
      </div>

      <!-- JSON File Upload -->
      <div class="mb-3">
        <label for="file-input" class="form-label">Upload JSON File</label>
        <input
            type="file"
            id="file-input"
            class="form-control"
            @change="handleFileUpload"
            accept=".json"
        />
      </div>

      <!-- JSON Textarea Input -->
      <div class="mb-3">
        <label for="json-input" class="form-label">Or Paste JSON Data</label>
        <textarea
            id="json-input"
            v-model="jsonInput"
            class="form-control"
            rows="10"
            placeholder="Enter JSON configuration"
        ></textarea>
      </div>

      <button @click="previewForm" class="btn btn-primary mt-3">
        Preview Form
      </button>
    </div>

    <!-- Form Preview -->
    <div v-if="formFields.length > 0" class="mb-4">
      <h2 class="fw-bold">Form Preview</h2>
      <h3 class="mb-3">{{ formTitle || "Untitled Form" }}</h3>
      <form>
        <div v-for="(field, index) in formFields" :key="index" class="mb-3">
          <label :for="'field-' + index" class="form-label">{{ field.label }}</label>
          <input
              v-if="field.type === 'text' || field.type === 'email'"
              :type="field.type"
              :id="'field-' + index"
              :placeholder="field.placeholder"
              class="form-control"
          />
          <textarea
              v-else-if="field.type === 'textarea'"
              :id="'field-' + index"
              :placeholder="field.placeholder"
              class="form-control"
              rows="3"
          ></textarea>
          <span class="text-danger" v-if="field.required">*</span>
        </div>
      </form>
    </div>

    <!-- Save Configuration Button -->
    <button
        v-if="formFields.length > 0"
        @click="saveConfiguration"
        class="btn btn-success"
    >
      Save Configuration
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { useMyFetch } from '@/composables/useMyFetch';

const jsonInput = ref('');
const formFields = ref([]);
const formTitle = ref(''); // New state for form title

// Handle file upload and parse JSON
const handleFileUpload = (event) => {
  const file = event.target.files[0];
  if (!file) {
    alert('No file selected.');
    return;
  }

  const reader = new FileReader();
  reader.onload = (e) => {
    try {
      const parsedJson = JSON.parse(e.target.result);
      jsonInput.value = JSON.stringify(parsedJson, null, 2); // Update the textarea with the parsed JSON
      formTitle.value = parsedJson.title || ''; // Update the form title from JSON if available
      previewForm(); // Automatically preview the form
    } catch (error) {
      alert('Invalid JSON file format.');
    }
  };
  reader.readAsText(file);
};

// Parse JSON and preview form
const previewForm = () => {
  try {
    const parsedJson = JSON.parse(jsonInput.value);

    // Validate fields
    if (!Array.isArray(parsedJson.fields)) {
      alert('Invalid JSON: "fields" should be an array.');
      return;
    }

    formTitle.value = parsedJson.title || formTitle.value; // Set title if provided in JSON
    formFields.value = parsedJson.fields;
  } catch (error) {
    alert('Invalid JSON format. Please correct and try again.');
  }
};

// Save configuration to the backend
const saveConfiguration = async () => {
  try {
    const response = await useMyFetch('forms', {
      method: 'POST',
      body: {
        title: formTitle.value,
        fields: formFields.value,
      },
    });

    if (response.status) {
      alert('Configuration saved successfully!');
    } else {
      alert('Failed to save configuration.');
    }
  } catch (error) {
    console.error('Error saving configuration:', error);
    alert('An error occurred. Please try again.');
  }
};
</script>

<style>
textarea {
  font-family: monospace;
}
</style>
