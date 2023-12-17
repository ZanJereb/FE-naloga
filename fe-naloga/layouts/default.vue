<template>
  <div>
    <aside
      id="default-sidebar" class="fixed top-0 left-0 z-40 w-64 h-screen transition-transform -translate-x-full sm:translate-x-0 bg-[#06080f] border-r-2 border-[#313442]"
    >
      <div class="p-6 lg:px-8">
        <span>
          <img class="logo" src="/assets/apillon.png" alt="Apillon">
        </span>
      </div>
    </aside>

    <div class="p-4 h-screen sm:ml-64 bg-[#06080f]">
      <template>
        <div>
          Layout: default
          <slot />
        </div>
      </template>
    </div>
  </div>
</template>

<script>
export default {
}
</script>

<style>
.logo{
  height: 24px;
  vertical-align: middle;
}
</style>
