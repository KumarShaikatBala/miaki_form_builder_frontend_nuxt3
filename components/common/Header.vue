<script setup lang="js">
import {useAuthStore} from "../../stores/auth";
</script>

<template>
  <aside class="main-sidebar sidebar-dark-primary elevation-4">
    <a href="#" class="brand-link text-center">
      <span class="brand-text font-weight-light">Miaki</span>
    </a>
    <div class="sidebar">
      <div class="user-panel mt-3 pb-3 mb-3 d-flex">
        <div class="info" v-if="useAuthStore().isAuthenticated">
          <a href="#" class="d-block">{{ useAuthStore().user.name }}</a>
        </div>
      </div>
      <nav class="mt-2">
        <ul class="nav nav-pills nav-sidebar flex-column" data-widget="treeview" role="menu" data-accordion="false">
          <li v-if="useAuthStore().isAuthenticated" class="nav-item">
            <button class="nav-link" data-toggle="dropdown" @click="useAuthStore().logout()">
            Log Out  <i class="fa fa-sign-out-alt"></i>
            </button>
          </li>
          <li class="nav-item">
            <nuxt-link to="/create" class="nav-link">
              <i class="nav-icon fas fa-file"></i>
              <p>Create Form</p>
            </nuxt-link>
          </li>
          <li class="nav-item">
            <nuxt-link to="/json" class="nav-link">
              <i class="nav-icon fas fa-file"></i>
              <p>Create Form Using json File or json data</p>
            </nuxt-link>
          </li>
          <li class="nav-item">
            <nuxt-link to="/forms" class="nav-link">
              <i class="nav-icon fas fa-file"></i>
              <p>Forms</p>
            </nuxt-link>
          </li>
        </ul>
      </nav>
      <!-- /.sidebar-menu -->
    </div>
    <!-- /.sidebar -->
  </aside>
</template>