<template>
  <h1>Events for Good</h1>
  <div class="events">
    <EventCard v-for="event in events" :key="event.id" :event="event" />
    <div class="pagination">
      <router-link
        id="page-prev"
        :to="{ name: 'EventList', query: { page: page - 1 } }"
        rel="prev"
        v-if="page != 1"
        >&#60; Previous
      </router-link>
      <router-link
        :to="{ name: 'EventList', query: { page: page + 1 } }"
        rel="next"
        v-if="hasNextPage"
        >Next &#62;
      </router-link>
    </div>
  </div>
</template>

<script>
import EventCard from '@/components/EventCard.vue'
import EventService from '@/services/EventService.js'

export default {
  name: 'EventList',
  props: {
    page: {
      type: Number,
      default: 1
    }
  },
  components: {
    EventCard
  },
  data() {
    return {
      events: null,
      totalEvents: 0
    }
  },
  /* ---------------- Global route guards -------------------*/
  // router.beforeEach((to, from, next) =>{}) -> called before each navigation and before in-component guards
  // router.beforeResolve((to, from, next) => {}) -> called before each navigation, but after in-component guards
  // router.afterEach((to, from) => {}) -> called after navigation is complete, don't have a next function and can't affect the navigation

  /* ---------------- In-component route guards -------------------*/
  beforeRouteEnter(routeTo, routeFrom, next) {
    // called when a route is entered from a different one
    // here we have not access to 'this'
    // return a promise if you require that vue-router wait for the API call
    return EventService.getEvents(2, parseInt(routeTo.query.page) || 1)
      .then(response => {
        next(comp => {
          // here we have access to the component 'comp'
          comp.events = response.data
          comp.totalEvents = response.headers['x-total-count']
        })
      })
      .catch(error => {
        console.log(error)
        next({ name: 'NetworkError' })
      })
  },
  beforeRouteUpdate(routeTo) {
    // called when route changes, but the component has not
    // here we have access to 'this'
    // use case: we have to reuse the same component
    // return a promise if you require that vue-router wait for the API call
    return EventService.getEvents(2, parseInt(routeTo.query.page) || 1)
      .then(response => {
        this.events = response.data
        this.totalEvents = response.headers['x-total-count']
      })
      .catch(error => {
        console.log(error)
        return { name: 'NetworkError' }
      })
  },
  beforeRouteLeave() {
    // called when route is navigated away from
  },
  computed: {
    hasNextPage() {
      const lastPage = Math.ceil(this.totalEvents / 2)
      return this.page < lastPage
    }
  }
}
</script>

<style scoped>
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.pagination {
  display: flex;
  width: 290px;
}
.pagination a {
  flex: 1;
  text-decoration: none;
  color: #2c3e50;
}

#page-prev {
  text-align: left;
}

#page-next {
  text-align: right;
}
</style>
