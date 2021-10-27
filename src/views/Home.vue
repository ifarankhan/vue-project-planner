<template>
  <div class="home">
    <div class="filter-nav">
      <FilterNavs :current="current" @filterChange="handleFilters" />
    </div>
    <div v-if="projects.length">
      <div v-for="project in filteredProjects" :key="project.id">
        <SingleProject
          :project="project"
          @deleted="handleDelete"
          @complete="handleComplete"
        />
      </div>
    </div>
    <div v-else>
      Nothing to show here
    </div>
  </div>
</template>

<script>
import SingleProject from "../components/SingleProject.vue";
import FilterNavs from "../components/FilterNavs.vue";
export default {
  name: "Home",
  components: { SingleProject, FilterNavs },
  data() {
    return {
      projects: [],
      masterProjects: [],
      current: "all",
    };
  },
  mounted() {
    fetch("http://localhost:3000/projects")
      .then((res) => res.json())
      .then((data) => (this.projects = data , this.masterProjects = data))
      .then((err) => console.log(err.message));
  },
  methods: {
    handleDelete(projectid) {
      this.projects = this.projects.filter((project) => {
        return project.id !== projectid;
      });
    },
    handleComplete(id) {
      let p = this.projects.find((project) => {
        return project.id === id;
      });
      p.complete = !p.complete;
    },
    handleFilters(currentClicked) {
      this.current = currentClicked;
      // if (currentClicked === "completed") {
      //   this.projects = this.masterProjects.filter((project) => {
      //     return project.complete === true;
      //   });
      // } else if (currentClicked === "ongoing"){
      //   this.projects = this.masterProjects.filter((project) => {
      //     return project.complete === false;
      //   });
      // }else{
      //   this.projects =this.masterProjects;
      // }
    },
  },
  computed: {
    filteredProjects() {
      if (this.current === 'completed') {
        return this.projects.filter((project) => {
          return project.complete === true;
        });
      }
      if (this.current === 'ongoing') {
        return  this.projects.filter((project) => {
          return project.complete === false;
        });
      }
      return this.projects
    }
  },
};
</script>
