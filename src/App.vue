<template>
  <html>
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1" />
      <title>Image Library</title>
    </head>
    <body>
      <div id="app">
        <div id="title">
          Photo Library
          <div class="action-container">
            <input id="search" type="text" placeholder="Busca" />
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 24 24"
              class="active"
            >
              <g fill-rule="evenodd">
                <path d="M0 0h24v24H0z" fill="none" />
                <path
                  d="M3 3v8h8V3H3zm6 6H5V5h4v4zm-6 4v8h8v-8H3zm6 6H5v-4h4v4zm4-16v8h8V3h-8zm6 6h-4V5h4v4zm-6 4v8h8v-8h-8zm6 6h-4v-4h4v4z"
                />
              </g>
            </svg>
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24">
              <path d="M0 0h24v24H0V0z" fill="none" />
              <path
                d="M4 10.5c-.83 0-1.5.67-1.5 1.5s.67 1.5 1.5 1.5 1.5-.67 1.5-1.5-.67-1.5-1.5-1.5zm0-6c-.83 0-1.5.67-1.5 1.5S3.17 7.5 4 7.5 5.5 6.83 5.5 6 4.83 4.5 4 4.5zm0 12c-.83 0-1.5.68-1.5 1.5s.68 1.5 1.5 1.5 1.5-.68 1.5-1.5-.67-1.5-1.5-1.5zM7 19h14v-2H7v2zm0-6h14v-2H7v2zm0-8v2h14V5H7z"
              />
            </svg>
          </div>
        </div>
        <PhotoCard
          :data="photos"
          @wheel.passive="infiniteScroll"
          @scroll="handleScroll"
        />
        <DataLoading v-if="loading" />
      </div>
    </body>
  </html>
</template>

<script>
import axios from "axios";
import DataLoading from "./components/DataLoading.vue";
import PhotoCard from "./components/PhotoCard.vue";

export default {
  name: "App",
  components: {
    PhotoCard,
    DataLoading,
  },
  data() {
    return {
      photos: null,
      photosForFetch: 20,
      pages: 1,
      waitScroll: false,
      infinite: true,
      loading: true,
    };
  },
  methods: {
    fetchData() {
      axios
        .get("https://jsonplaceholder.typicode.com/photos")
        .then((r) => {
          this.photos = r.data.filter((photo) => photo.id <= this.photosToView);
          this.loading = false;
        })
        .catch((error) => {
          alert(error);
        });
    },
    infiniteScroll() {
      if (this.infinite) {
        const scrolling =
          document.body.scrollHeight -
          window.scrollY -
          Math.max(document.body.offsetHeight, window.innerHeight) * 1.01;
        if (scrolling < 0 && !this.waitScroll) {
          this.loading = true;
          this.pages++;
          this.waitScroll = true;
          setTimeout(() => this.fetchData(), 500);
          setTimeout(() => {
            this.waitScroll = false;
          }, 500);
        }
      }
    },
    handleScroll() {
      this.infiniteScroll();
    },
  },
  computed: {
    photosToView() {
      return this.photosForFetch * this.pages;
    },
  },
  created() {
    this.fetchData();
    window.addEventListener("scroll", this.handleScroll);
  },
  unmounted() {
    window.removeEventListener("scroll", this.handleScroll);
  },
};
</script>

<style>
@import "./assets/page.css";
</style>
