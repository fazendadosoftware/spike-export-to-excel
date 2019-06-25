<template>
  <div id="app">
    <button @click="exportToExcel">Export to Excel</button>
    <div v-for="factsheet in dataset" :key="factsheet.id">
      {{factsheet.name}}
    </div>
  </div>
</template>

<script>
import XLSX from 'xlsx'

export default { /* eslint-disable */
  name: 'app',
  data () {
    return {
      dataset: []
    }
  },
  methods: {
    exportToExcel () {
      const workbook = XLSX.utils.book_new() // A workbook is the name given to an Excel file
      const worksheet = XLSX.utils.json_to_sheet(this.dataset) // export json to an excel worksheet
      XLSX.utils.book_append_sheet(workbook, worksheet, 'factsheets') // add worksheet to workbook
      XLSX.writeFile(workbook, 'book.xlsx') // export excel file
    }
  },
  created () {
    this.$lx.init()
      .then(setup => { /* eslint-disable */
        console.debug(`report setup`, setup)
        this.$lx.ready({})
      })
    /* fetch all workspace factsheets for building our demo dataset */
    const query = `{allFactSheets{edges{node{id type name}}}}`
    this.$lx.executeGraphQL(query)
      .then(res => {
        this.dataset = res.allFactSheets.edges
          .map(edge => edge.node)
      })
  }
}
</script>
