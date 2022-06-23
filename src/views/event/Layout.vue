<template>
  <div v-if="GStore.event">
    <h1>{{ GStore.event.title }}</h1>
    <div id="nav">
      <router-link :to="{ name: 'EventDetails', params: { id } }"
        >Details
      </router-link>
      <router-link :to="{ name: 'EventRegister', params: { id } }"
        >Register
      </router-link>
      <router-link :to="{ name: 'EventEdit', params: { id } }"
        >Edit
      </router-link>
    </div>
    <router-view :event="GStore.event"></router-view>
  </div>
</template>

<script>
// import EventService from '@/services/EventService.js'
export default {
  props: ['id'],
  inject: ['GStore']
  // data() {
  //   return {
  //     event: null
  //   }
  // },
  // beforeRouteEnter(routeTo, routeFrom, next) {
  //   return EventService.getEvent(parseInt(routeTo.params.id))
  //     .then(response => {
  //       next(comp => {
  //         comp.event = response.data
  //       })
  //     })
  //     .catch(error => {
  //       console.log(error)
  //       if (error.response && error.response.status == 404) {
  //         next({
  //           name: '404Resource',
  //           params: { resource: 'event' }
  //         })
  //       } else {
  //         next({ name: 'NetworkError' })
  //       }
  //     })
  // }
}
</script>
