<script setup lang="ts"></script>

<template>
  <header>
    <div class="links">
      <a href="https://www.instagram.com/rap.falando/" target="_blank" rel="noopener noreferrer">
        <img src="/instagram.png" />
        @rap.falando
      </a>
    </div>
    <img src="/header.jpg" />
  </header>
</template>

<style scoped>
header {
  margin: 48px 0;
}

header > img {
  /* height: 128px; */
  width: 100%;
  max-height: 128px;
}

.links {
  margin-bottom: 16px;
  padding: 0 8px;
}

.links a {
  color: white;
  text-decoration: none;
  display: flex;
  align-items: center;
  font-weight: 500;
}

.links img {
  margin-right: 4px;
  height: 32px;
}
</style>
