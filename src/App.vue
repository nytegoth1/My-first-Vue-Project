<template>
  <div id="app" class="container-fluid">
    <header>
      <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
        <div class="container-fluid">
          <a class="navbar-brand" href="#">Top News</a>
          <button
            class="navbar-toggler"
            type="button"
            data-bs-toggle="collapse"
            data-bs-target="#navbarCollapse"
            aria-controls="navbarCollapse"
            aria-expanded="false"
            aria-label="Toggle navigation"
          >
            <span class="navbar-toggler-icon"></span>
          </button>
          <div class="collapse navbar-collapse" id="navbarCollapse">
            <ul class="navbar-nav me-auto mb-2 mb-md-0">
              <li class="nav-item active">
                <a class="nav-link" aria-current="page" href="#">Home</a>
              </li>
              <li class="nav-item">
                <a class="nav-link" href="#">Link</a>
              </li>
            </ul>

            <input
              class="form-control"
              type="text"
              v-model="searchQuery"
              placeholder="Search"
            />
          </div>
        </div>
      </nav>
    </header>

    <span></span>
    <span class="nosource" v-if="!resultQuery.length">No items found</span>
    <div class="panel panel-default">
      <div class="panel-heading">
        <strong></strong>
      </div>
      <div class="row">
        <div class="search-wrapper panel-heading col-sm-12"></div>
      </div>
      <div v-for="item in resultQuery" v-if="resources.length">
        <div class="col-md-7">
          <h2 class="featurette-heading">
            <a :href="`${item.url}`" target="_blank">
              <p class="source">{{ item.source.name }}</p>
            </a>
            <span class="title">{{ item.title }}</span>
          </h2>
          <p class="lead">
            {{ item.description }}
            <span class="text-muted"
              ><a :href="`${item.url}`" target="_blank">Read more >></a></span
            >
          </p>
        </div>

        <div class="col-md-7 izoom">
          <span class="picture"
            ><a :href="`${item.url}`" target="_blank"
              ><img :src="item.urlToImage" /></a
          ></span>
          <hr class="featurette-divider" />
        </div>
      </div>
    </div>
    <h3>page {{ page }} of {{ pages.length }}</h3>
    <nav aria-label="Page navigation example">
      <ul class="pagination">
        <li class="page-item">
          <a class="page-link" href="#" @click="page = 1" aria-label="First">
            <span aria-hidden="true">&laquo;</span>
          </a>
        </li>
        <li class="page-item">
          <a
            class="page-link"
            href="#"
            v-if="page != 1"
            @click="page--"
            aria-label="Previous"
          >
            <span aria-hidden="true">&lsaquo;</span>
          </a>
        </li>
        <li
          class="page-item"
          v-for="pageNumber in pages.slice(page - 1, page + 4)"
          :class="{ active: page === pageNumber }"
        >
          <a class="page-link" href="#" @click="page = pageNumber">{{
            pageNumber
          }}</a>
        </li>
        <li class="page-item">
          <a
            class="page-link"
            href="#"
            @click="page++"
            v-if="page < pages.length"
            aria-label="Next"
          >
            <span aria-hidden="true">&rsaquo;</span>
          </a>
        </li>
        <li class="page-item">
          <a
            class="page-link"
            href="#"
            @click="page = pages.length"
            aria-label="Last"
          >
            <span aria-hidden="true">&raquo;</span>
          </a>
        </li>
      </ul>
    </nav>
  </div>
</template>

<script>
import axios from "axios";
import styles from "./styles/style.css";
export default {
  name: "app",
  data() {
    return {
      searchQuery: null,
      url: "top-headlines.json",
      //url: "download.json",
      resources: [],
      page: 1,
      perPage: 2,
      pages: [],
    };
  },
  methods: {
    getNewsfeed() {
      axios
        .get(this.url)
        .then((response) => {
          this.resources = response.data.articles;
          console.log(this.resources);
        })
        .catch((error) => {
          console.log(error);
          this.errored = true;
        })
        .finally(() => (this.loading = false));
    },
    setPages() {
      var numberOfPages = Math.ceil(this.resources.length / this.perPage);
      for (var index = 1; index <= numberOfPages; index++) {
        this.pages.push(index);
      }
    },
    paginate(resources) {
      var page = this.page;
      var perPage = this.perPage;
      var from = page * perPage - perPage;
      var to = page * perPage;
      return resources.slice(from, to);
    },
  },
  created() {
    this.getNewsfeed();
  },
  watch: {
    resources() {
      this.setPages();
    },
  },
  computed: {
    filteredItems: function () {
      return this.$eval("items | filterBy searchQuery");
    },
  },
  computed: {
    resultQuery() {
      if (this.searchQuery) {
        return this.resources.filter((item) => {
          //Commented the line out below for use with download.json movie search
          //return this.searchQuery.toLowerCase().split(' ').every(v => item.Title.toLowerCase().includes(v) + item.Plot.toLowerCase().includes(v) + item.Actors.toLowerCase().includes(v) + item.Rated.toLowerCase().includes(v))
          return this.searchQuery
            .toLowerCase()
            .split(" ")
            .every(
              (v) =>
                item.title.toLowerCase().includes(v) +
                item.source.name.toLowerCase().includes(v) +
                item.description.toLowerCase().includes(v)
            );
        });
      } else {
        return this.paginate(this.resources);
      }
    },
  },
};
</script>