<template>
  <div>
    <b-navbar toggleable="lg" type="dark" variant="info" no-collapse>
      <b-navbar-brand>DataCose Challange</b-navbar-brand>

      <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

      <b-collapse id="nav-collapse" is-nav>
        <b-navbar-nav>
          <b-nav-item to="/posts">Posts</b-nav-item>
          <b-nav-item to="/todos">Todos</b-nav-item>
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>
  </div>
</template>

<script>
export default {
  methods: {},
};
</script>
