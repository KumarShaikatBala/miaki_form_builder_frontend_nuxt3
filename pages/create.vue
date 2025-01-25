<template>
  <div class="container py-5">
    <h1 class="mb-4">Create New Form</h1>

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
          <div class="list-group-item">
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

<script lang="ts" setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import draggable from 'vuedraggable';
import { useMyFetch } from '@/composables/useMyFetch';

interface ValidationRules {
  minLength?: number;
  maxLength?: number;
  pattern?: string;
}

interface FormField {
  type: string;
  label: string;
  placeholder?: string;
  required: boolean;
  validation_rules: ValidationRules;
  id?: number; // Temporary ID for client-side operations
}

interface Form {
  title: string;
  fields: FormField[];
}

const router = useRouter();

const form = ref<Form>({
  title: '',
  fields: []
});

const newField = ref<FormField>({
  type: 'text',
  label: '',
  placeholder: '',
  required: false,
  validation_rules: {}
});

const addField = () => {
  if (!newField.value.label || !newField.value.type) {
    alert('Please provide a label and type for the new field.');
    return;
  }

  form.value.fields.push({
    ...newField.value,
    id: Date.now(), // Temporary ID for client-side operations
    validation_rules: { ...newField.value.validation_rules }
  });

  newField.value = {
    type: 'text',
    label: '',
    placeholder: '',
    required: false,
    validation_rules: {}
  };
};

const removeField = (index: number) => {
  form.value.fields.splice(index, 1);
};

const updateFieldOrder = () => {
  console.log('Field order updated:', form.value.fields);
};

const submitForm = async () => {
  try {
    const response = await useMyFetch('forms', {
      method: 'POST',
      body: {
        title: form.value.title,
        fields: form.value.fields.map((field) => ({
          type: field.type,
          name: field.label.toLowerCase().replace(/\s+/g, '_'),
          label: field.label,
          placeholder: field.placeholder,
          required: field.required,
          validation_rules: field.validation_rules
        }))
      }
    });

    if (response.status) {
      alert('Form created successfully!');
      router.push('/forms');
    } else {
      alert('Failed to create form.');
    }
  } catch (error) {
    console.error('Error creating form:', error);
    alert('Failed to create form. Please try again.');
  }
};
</script>

<style>
.list-group-item {
  cursor: grab;
}
</style>
