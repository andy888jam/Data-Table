<template>
  <div>
    <SearchBar @termChange="onTermChange"/>
    <Table/>
  </div>
</template>

<script>
import SearchBar from './components/SearchBar'
import Table from './components/Table'


export default {
 name: 'App',
 components: {
  SearchBar,
  Table
 },
}
</script>