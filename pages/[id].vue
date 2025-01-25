<template>
  <div class="container py-5">
    <h1 class="mb-4">Dynamic Form Builder</h1>

    <!-- Form Title -->
    <div class="mb-4">
      <label for="form-title" class="form-label fw-bold">Form Title</label>
      <input
          id="form-title"
          v-model="form.title"
          type="text"
          class="form-control"
          placeholder="Enter Form Title"
      />
    </div>

    <!-- Dynamic Form Fields -->
    <div class="mb-4">
      <h2 class="fw-bold">Form Fields</h2>
      <draggable
          v-model="form.fields"
          group="fields"
          @end="updateFieldOrder"
          class="list-group"
      >
        <template #item="{ element, index }">
          <div
              class="list-group-item"
          >
            <!-- Editable Fields -->
            <div class="row g-3 align-items-center mb-3">
              <div class="col-md-3">
                <label class="form-label">Label</label>
                <input
                    v-model="element.label"
                    type="text"
                    class="form-control"
                    placeholder="Field Label"
                />
              </div>
              <div class="col-md-2">
                <label class="form-label">Type</label>
                <select v-model="element.type" class="form-select">
                  <option value="text">Text</option>
                  <option value="email">Email</option>
                  <option value="textarea">Textarea</option>
                </select>
              </div>
              <div class="col-md-3">
                <label class="form-label">Placeholder</label>
                <input
                    v-model="element.placeholder"
                    type="text"
                    class="form-control"
                    placeholder="Placeholder"
                />
              </div>
              <div class="col-md-2">
                <label class="form-label">Required</label>
                <div class="form-check">
                  <input
                      v-model="element.required"
                      type="checkbox"
                      class="form-check-input"
                      :id="'required-' + index"
                  />
                  <label class="form-check-label" :for="'required-' + index">
                    Yes
                  </label>
                </div>
              </div>
              <div class="col-md-2">
                <label class="form-label">Actions</label>
                <button
                    @click.prevent="removeField(index)"
                    class="btn btn-danger btn-sm w-100"
                >
                  Remove
                </button>
              </div>
            </div>

            <!-- Validation Rules -->
            <div class="row g-3 align-items-center">
              <div class="col-md-3" v-if="element.type === 'text' || element.type === 'textarea'">
                <label class="form-label">Min Length</label>
                <input
                    v-model="element.validation_rules.minLength"
                    type="number"
                    class="form-control"
                    placeholder="Min Length"
                />
              </div>
              <div class="col-md-3" v-if="element.type === 'text' || element.type === 'textarea'">
                <label class="form-label">Max Length</label>
                <input
                    v-model="element.validation_rules.maxLength"
                    type="number"
                    class="form-control"
                    placeholder="Max Length"
                />
              </div>
              <div class="col-md-6" v-if="element.type === 'email'">
                <label class="form-label">Pattern</label>
                <input
                    v-model="element.validation_rules.pattern"
                    type="text"
                    class="form-control"
                    placeholder="Validation Pattern"
                />
              </div>
            </div>
          </div>
        </template>
      </draggable>
    </div>

    <!-- Add New Field -->
    <div class="mb-4">
      <h3 class="fw-bold">Add New Field</h3>
      <div class="row g-3">
        <div class="col-md-3">
          <input
              v-model="newField.label"
              type="text"
              class="form-control"
              placeholder="Field Label"
          />
        </div>
        <div class="col-md-2">
          <select v-model="newField.type" class="form-select">
            <option value="text">Text</option>
            <option value="email">Email</option>
            <option value="textarea">Textarea</option>
          </select>
        </div>
        <div class="col-md-3">
          <input
              v-model="newField.placeholder"
              type="text"
              class="form-control"
              placeholder="Placeholder"
          />
        </div>
        <div class="col-md-2">
          <div class="form-check">
            <input
                v-model="newField.required"
                type="checkbox"
                class="form-check-input"
                id="new-required"
            />
            <label class="form-check-label" for="new-required">Required</label>
          </div>
        </div>
        <div class="col-md-2">
          <button @click="addField" class="btn btn-success w-100">
            Add Field
          </button>
        </div>
      </div>
    </div>

    <!-- Submit Button -->
    <div>
      <button @click="submitForm" class="btn btn-primary btn-lg">
        Save Form
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRouter, useRoute } from 'nuxt/app';
import draggable from 'vuedraggable';
import { useMyFetch } from '@/composables/useMyFetch';

const form = ref({
  id: null,
  title: '',
  fields: []
});

const newField = ref({
  type: 'text',
  label: '',
  placeholder: '',
  required: false,
  validation_rules: {}
});

const router = useRouter();
const route = useRoute();

// Fetch the form data
onMounted(async () => {
  const { id } = route.params;
  try {
    const response = await useMyFetch(`forms/${id}`);
    form.value = response.data;
  } catch (error) {
    console.error('Error fetching form data:', error);
  }
});

// Add a new field
const addField = () => {
  if (!newField.value.label || !newField.value.type) {
    alert('Please provide a label and type for the new field.');
    return;
  }

  form.value.fields.push({
    ...newField.value,
    id: Date.now(), // Temporary ID
    validation_rules: newField.value.validation_rules || {}
  });

  // Reset newField
  newField.value = {
    type: 'text',
    label: '',
    placeholder: '',
    required: false,
    validation_rules: {}
  };
};

// Remove a field
const removeField = (index) => {
  form.value.fields.splice(index, 1);
};

// Update field order
const updateFieldOrder = () => {
  console.log('Field order updated:', form.value.fields);
};

// Submit the form
const submitForm = async () => {
  try {
    await useMyFetch(`forms/${form.value.id}`, {
      method: 'PUT',
      body: {
        title: form.value.title,
        fields: form.value.fields.map((field) => ({
          type: field.type,
          name: field.name || field.label.toLowerCase().replace(/\s+/g, '_'),
          label: field.label,
          placeholder: field.placeholder,
          required: field.required,
          validation_rules: field.validation_rules
        }))
      }
    });
    alert('Form saved successfully!');
    router.push('/forms');
  } catch (error) {
    console.error('Error saving form:', error);
    alert('Failed to save the form. Please try again.');
  }
};
</script>

<style>
.list-group-item {
  cursor: grab;
}
</style>
