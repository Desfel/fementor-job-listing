<template>
  <div class="page-wrapper">
    <section class="header-section"></section>

    <section class="filter-wrapper" :class="{'is-visible': tagsArray.length > 0}">
      <div class="filter-container">
        <div class="filters">
          <div class="filter" v-for="tag in tagsArray" v-bind:key="tag" >
            <p v-text="tag"></p>
            <span class="remove-tab" @click="removeTag(tag)">
              <img src="@/assets/img/icon-remove.svg" alt="Remove Filter" />
            </span>
          </div>
        </div>

        <a class="clear-filters" href="#" @click="clearAllTags">Clear</a>
      </div>
    </section>

    <section class="job-listing-wrapper">
      <div v-for="job in jobs"
           v-bind:key="job.id"
           :class="{
             'is-featured': job.featured,
             'is-visible': (tagsArray.length == 0 || visibleJobs.indexOf(job.id) !== -1 )}"
           class="job-listing"
      >
        <div class="job-content-wrapper">
          <img :src="job.logo" />

          <div class="job-content">
            <div class="job-title-row">
              <p v-text="job.company"></p>

              <span v-if="job.new" class="new-job">New!</span>
              <span v-if="job.featured" class="featured-job">Featured</span>
            </div>

            <p v-text="job.position" class="job-role"></p>

            <div class="job-info-wrapper">
              <p v-text="job.postedAt"></p>
              <span class="separator"></span>
              <p v-text="job.contract"></p>
              <span class="separator"></span>
              <p v-text="job.location"></p>
            </div>
          </div>
        </div>
        <div class="job-filters">
          <p v-text="job.role" @click="addTags(job.role)"></p>
          <p v-text="job.level" @click="addTags(job.level)"></p>
          <p v-for="tag in job.languages" v-bind:key="tag" @click="addTags(tag)">{{ tag }}</p>
          <p v-for="tag in job.tools" v-bind:key="tag" @click="addTags(tag)">{{ tag }}</p>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import { mapState } from 'vuex'
import jobsList from '../json/data.json'

export default {
  data() {
    return {
      jobs: jobsList,
      tagsArray: [],
      visibleJobs: []
    }
  },
  head() {
    return {
      title: {
        inner: 'Home'
      },
      meta: [
        {
          name: 'description',
          content: `${this.appTitle} home page`,
          id: 'desc'
        }
      ]
    }
  },
  methods: {
    addTags(value) {
      if (this.tagsArray.indexOf(value) === -1) {
        this.tagsArray.push(value)
      }

      this.refreshVisibleJobs('add')
      console.log(this.visibleJobs)
    },
    removeTag(value) {
      this.tagsArray.splice(this.tagsArray.indexOf(value), 1)
      this.refreshVisibleJobs('remove')
    },
    clearAllTags(e) {
      e.preventDefault()
      this.tagsArray = []
      this.visibleJobs = []
    },
    refreshVisibleJobs() {
      this.jobs.forEach(job => {
        const jobLevel = job.level
        const jobRole = job.role
        const jobLanguages = job.languages
        const jobTools = job.tools
        const jobFilters = [jobLevel, jobRole, ...jobLanguages, ...jobTools]
        let shouldUpdate = true

        this.tagsArray.forEach(tag => {
          if(jobFilters.indexOf(tag) === -1) {
            shouldUpdate = false
          }
        })

        if (shouldUpdate) {
          if (this.visibleJobs.indexOf(job.id) === -1) {
            // console.log(`Should add Add ${job.id}`)
            this.visibleJobs.push(job.id)
          }
        } else if(this.visibleJobs.indexOf(job.id) !== -1) {
          this.visibleJobs.splice(this.visibleJobs.indexOf(job.id), 1)
        }
      })
    }
  },
  computed: mapState('app', ['appTitle'])
}
</script>

<style lang="scss" scoped>
@import '@/theme/variables.scss';
@import '@/theme/main.scss';

.page-wrapper {
  display: flex;
  flex-direction: column;
  height: 100%;
  min-height: 100vh;
  background: $neutralColor1;
}
</style>
