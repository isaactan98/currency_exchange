<template>
  <div class="navbar bg-base-100 sticky top-0" id="nav">
    <div class="flex-1">
      <img src="~/assets/images/logo.png" alt="" class="w-12" srcset="" />
    </div>
    <div class="flex-1 hidden lg:block text-center">
      <h1 class="font-bold">Exchange Currency</h1>
    </div>
    <div class="flex-1 dropdown dropdown-end text-right">
      <button class="btn btn-square btn-ghost" tabindex="0">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          class="inline-block w-5 h-5 stroke-current"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="M5 12h.01M12 12h.01M19 12h.01M6 12a1 1 0 11-2 0 1 1 0 012 0zm7 0a1 1 0 11-2 0 1 1 0 012 0zm7 0a1 1 0 11-2 0 1 1 0 012 0z"
          ></path>
        </svg>
      </button>
      <ul
        tabindex="0"
        class="dropdown-content menu p-2 shadow bg-base-100 rounded-box w-52"
      >
        <li>
          <a href="https://portfolio-isaactan98.vercel.app/">
            Made By Isaac Tan
          </a>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  mounted() {
    // add shadow to navbar when scrolling
    window.addEventListener("scroll", () => {
      const nav = document.getElementById("nav");
      if (window.scrollY > 0) {
        nav.classList.add("shadow-md");
      } else {
        nav.classList.remove("shadow-md");
      }
    });
  },
};
</script>

<style>
#nav {
  transition: all 0.3s ease;
}
</style>
