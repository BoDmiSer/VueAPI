<template>
  <div class="list row">
    <div class="col-md-8">
      <div class="input-group mb-3">
        <input type="text" class="form-control" placeholder="Search by title" v-model="searchTitle" />
        <div class="input-group-append">
          <button class="btn btn-outline-secondary" type="button" @click="spage = 1; retrieveTutorials();">Search</button>
        </div>
      </div>
    </div>
         <div class="col-md-12">
      <div class="mb-3">
        Items per Page:
        <select v-model="pageSize" @change="handlePageSizeChange($event)">
          <option v-for="size in pageSizes" :key="size" :value="size">
            {{ size }}
          </option>
        </select>
      </div>

      <b-pagination
        v-model="page"
        :total-rows="count"
        :per-page="pageSize"
        prev-text="Prev"
        next-text="Next"
        @change="handlePageChange"
      ></b-pagination>
    </div>
    <div class="col-md-6">
      <h4>Published List</h4>
      <ul class="list-group">
        <li
          class="list-group-item"
          :class="{ active: index == currentIndex }"
          v-for="(tutorial,index) in tutorials"
          :key="index"
          @click="setActiveTutorial(tutorial,index)"
        >{{tutorial.title}}</li>
      </ul>
    </div>
    <div class="col-md-6">
      <div v-if="currentTutorial">
        <h4>Published tutorial</h4>
        <div>
          <label>
            <strong>Title:</strong>
          </label>
          {{currentTutorial.title}}
        </div>
        <div>
          <label>
            <strong>Description:</strong>
          </label>
          {{currentTutorial.description}}
        </div>
      </div>
      <div v-else>
        <br />
        <p>Please click on a Tutorial...</p>
      </div>
    </div>
  </div>
</template>

<script>
import TutorialDataService from "../services/TutorialDataService";

export default {
  name: "tutorials-list",
  data() {
    return {
      tutorials: [],
      currentTutorial: null,
      currentIndex: -1,
      title: "",
      
      searchTitle:"",
      
      page:1,
      count:0,
      pageSize: 4,

      pageSizes: [4, 8, 16],
    };
  },
  methods: {
     getRequestParams(searchTitle, page, pageSize) {
      let params = {};

      if (searchTitle) {
        params["title"] = searchTitle;
      }

      if (page) {
        params["page"] = page - 1;
      }

      if (pageSize) {
        params["size"] = pageSize;
      }

      return params;
    },
       retrieveTutorials() {
        const params = this.getRequestParams(
        this.searchTitle,
        this.page,
        this.pageSize
      );
       TutorialDataService.getAllPublished(params)
        .then((response) => {
          const { tutorials, totalItems } = response.data;
          this.tutorials = tutorials;
          this.count = totalItems;

          console.log(response.data);
        })
        .catch((e) => {
          console.log(e);
        });
    },
      handlePageChange(value) {
      this.page = value;
      this.retrieveTutorials();
    },

    handlePageSizeChange(event) {
      this.pageSize = event.target.value;
      this.page = 1;
      this.retrieveTutorials();
    },
    refreshList() {
      this.retrieveTutorials();
      this.currentTutorial = null;
      this.currentIndex = -1;
    },
    
    setActiveTutorial(tutorial, index) {
      this.currentTutorial = tutorial;
      this.currentIndex = index;
    },
    
   /*  searchTitle() {
      TutorialDataService.findByTitle(this.title)
        .then(response => {
          this.tutorials = response.data;
          console.log(response.data);
        })
        .catch(e => {
          console.log(e);
        });
    } */
  },mounted() {
    this.retrieveTutorials();
  }
}

</script>

<style>
.list {
  text-align: left;
  max-width: 750px;
  margin: auto;
}
</style>