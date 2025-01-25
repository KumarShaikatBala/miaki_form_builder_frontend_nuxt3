<script setup lang="js">
import { useAuthStore } from '~/stores/auth';
const authStore = useAuthStore();
/*const preloader = ref(true);
onMounted(() => {
  setTimeout(() => {
    preloader.value = false;
  }, 1000);
});*/
/*if (!authStore.isAuthenticated) {
  navigateTo('login');
}*/
</script>
<template>
  <body class="hold-transition sidebar-mini layout-fixed">
    <div class="wrapper">
      <!-- Preloader -->
<!--      <common-preloader :preloader="preloader"></common-preloader>-->
      <common-preloader ></common-preloader>
      <!-- Navbar -->
      <common-navbar></common-navbar>
      <!-- /.navbar -->
      <!-- Main Sidebar Container -->
      <common-header></common-header>
      <!-- Content Wrapper. Contains page content -->
      <div class="content-wrapper">
      <slot />
      </div>
      <!-- /.content-wrapper -->
      <common-footer></common-footer>
      <!-- Control Sidebar -->
      <aside class="control-sidebar control-sidebar-dark">
        <!-- Control sidebar content goes here -->
      </aside>
      <!-- /.control-sidebar -->
    </div>
  </body>
</template>
