<template>
  <div id="app">
    <WorkTime/>
    <TableShow/>
  </div>
</template>

<script>
import WorkTime from './components/WorkTime'
import TableShow from './components/TableShow'

export default {
  name: 'App',
  components: {
    WorkTime,
    TableShow
  }
}
</script>

<style>
#app {
  display: flex;
  justify-content: center;
  flex-wrap:wrap;
}
</style>
