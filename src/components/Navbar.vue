<template>
  <div class="center examplex">
    <vs-navbar
      :fixed="true"
      text-white 
      color="#3bdec8" 
      center-collapsed 
      v-model="active"
      >
      <template #left>
        <img src="https://img.icons8.com/fluent/50/000000/microsoft-todo-2019.png" />
      </template>
      <vs-navbar-item :active="active == 'guide'" id="guide">
        ToDO App
      </vs-navbar-item>
      <vs-navbar-item :active="active == 'docs'" id="docs">
        Documents
      </vs-navbar-item>
    </vs-navbar>
  </div>
</template>

<script>
export default {
  name: 'Navbar',
  data:() => ({
      active: 'guide'
  })
};
</script>