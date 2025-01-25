<template>
  <!-- Content Header (Page header) -->
  <section class="content-header">
    <div class="container-fluid">
      <div class="row mb-2">
        <div class="col-sm-6">
          <h1>Simple Forms List</h1>
        </div>
        <div class="col-sm-6">
          <ol class="breadcrumb float-sm-right">
            <li class="breadcrumb-item"><a href="#">Home</a></li>
            <li class="breadcrumb-item active">Forms List</li>
          </ol>
        </div>
      </div>
    </div>
  </section>

  <!-- Main content -->
  <section class="content">
    <div class="container-fluid">
      <div class="row">
        <div class="col-12">
          <div class="card">
            <div class="card-header">
              <h3 class="card-title">All Forms Titles</h3>
            </div>
            <!-- /.card-header -->
            <div class="card-body table-responsive p-0">
              <table class="table table-hover text-nowrap">
                <thead>
                <tr>
                  <th>SL</th>
                  <th>Title</th>
                  <th>Action</th>
                </tr>
                </thead>
                <tbody>
                <tr v-for="(data, index) in datas" :key="data.id">
                  <td>{{ index + 1 }}</td>
                  <td>{{ data.title }}</td>
                  <td>
                    <router-link
                        :to="{ name: 'id', params: { id: data.id } }"
                        class="btn btn-primary"
                    >
                      View
                    </router-link>
                    <button
                        @click="confirmDelete(data.id)"
                        class="btn btn-danger ms-2"
                    >
                      Delete
                    </button>
                  </td>
                </tr>
                </tbody>
              </table>
            </div>
            <!-- /.card-body -->
          </div>
          <!-- /.card -->
        </div>
      </div>
    </div>
  </section>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import { useMyFetch } from '@/composables/useMyFetch';

// Define the type for form data
interface FormData {
  id: number;
  title: string;
}

const datas = ref<FormData[]>([]);

// Fetch the forms from the backend
const getPosts = async () => {
  try {
    const response = await useMyFetch('forms');
    if (response?.data?.data) {
      datas.value = response.data.data;
    } else {
      console.error('Invalid response structure:', response);
    }
  } catch (error) {
    console.error('Error fetching forms:', error);
  }
};

// Confirm and delete a form
const confirmDelete = async (id: number) => {
  if (confirm('Are you sure you want to delete this form?')) {
    try {
      const response = await useMyFetch(`forms/${id}`, { method: 'DELETE' });
      if (response?.status) {
        alert('Form deleted successfully!');
        datas.value = datas.value.filter((form) => form.id !== id); // Update the list after deletion
      } else {
        alert('Failed to delete the form.');
      }
    } catch (error) {
      console.error('Error deleting form:', error);
      alert('An error occurred while deleting the form. Please try again.');
    }
  }
};

// Fetch forms on component load
getPosts();
</script>

<style>
.table .btn {
  margin-right: 5px;
}
</style>
