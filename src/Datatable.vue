<template>
  <div class="">
    <table class="table table table-bordered table-striped table-hover">
      <thead>
        <tr>
          <th
            v-for="column in columns"
            @click="toggleSort(column)"
            :key="column.field"
          > {{ column.label }}
            <span
              class="fa"
              v-if="column.sortable"
              :class="[ column.field != sort.by ? 'fa-sort' : [ !sort.ascending ? 'fa-sort-desc' : 'fa-sort-asc' ] ]"
            />
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-if="rows.length == 0">
          <td
            :colspan="columns.length"
            class="text-center">Empty table</td>
        </tr>
        <tr
          v-for="row in currentPage()"
          v-else>
          <td
            v-for="column in columns"
            :data-title="column.label"
            :key="column.field">
            <span v-if="column.format">
              <span
                v-if="column.format.type == 'money'"
                >{{formatMoney(resolve(row, column.field), column.format.symbol ? column.format.symbol : '₦')}}</span>
              <span v-else-if="column.format.type == 'date'">
                {{ parseDate(resolve(row, column.field), column.format.to) }}
              </span>
            </span>
            <span v-else-if="column.decoration">
              <router-link
                :to="parseLink(column.decoration.target, row)"
                v-if="column.decoration.type.includes('link')">
                <a>
                  <span v-if="column.decoration.type.includes('plain')">{{ column.field }}</span>
                  <span v-else> {{ resolve(row, column.field) }} </span>
                </a>
              </router-link>
            </span>
            <span v-else>{{ resolve(row, column.field, column.default) }}</span>
&nbsp;
          </td>
        </tr>
      </tbody>
    </table>
    <pagination :paginator="paginator" />
  </div>
</template>
<script>
  import Pagination from './Pagination'

  export default {
    components: {
      Pagination
    },
    props: {
      columns: {
        type: Array,
        required: true
      },
      dataset: {
        type: Array,
        required: true
      },
      search: {
        type: String,
        required: false,
        default: ''
      },
      indices: {
        type: Array,
        required: false,
        default: Array
      }
    },
    data () {
      return {
        paginator: {
          page: 1,
          count: 100,
          total: 0,
          available: 0
        },
        sort: {
          by: this.columns[0].field,
          ascending: true
        },
        rows: []
      }
    },
    mounted () {
      this.bubbleSort()
    },
    watch: {
      dataset: function () {
        this.bubbleSort()
      },
      'sort.ascending': function () {
        this.bubbleSort()
      },
      'sort.by': function () {
        this.bubbleSort()
      },
      search: function () {
        this.bubbleSort()
      }
    },
    methods: {
      formatMoney (value, symbol) {
        // value = Number(value)
        if (String(value) === '') {
          return '-'
        }
        return symbol + parseFloat(value).toFixed(2).replace(/(\d)(?=(\d{3})+(?:\.\d+)?$)/g, '$1,')
      },
      parseDate (date, format) {
        return this.$moment(date).format(format)
      },
      toggleSort (column) {
        if (column.sortable) {
          if (this.sort.by === column.field) {
            this.sort.ascending = !this.sort.ascending
          } else {
            this.sort.by = column.field
            this.sort.ascending = true
          }
        }
      },
      resolve (object, target, blank = '') {
        const nest = target.split('.')
        for (const n of nest) {
          object = object[n]
        }
        return object !== undefined ? object : blank
      },
      parseLink (url, object) {
        let placeholder = ''
        do {
          placeholder = url.substring(url.lastIndexOf(':'), url.lastIndexOf('/'))
          url = url.replace(placeholder, this.resolve(object, placeholder.substring(1)))
        } while (placeholder.length > 2 && url.includes(placeholder))
        return url
      },
      bubbleSort () {
        const rows = JSON.parse(JSON.stringify(this.dataset))
        if (rows.length === 0) {
          return
        }
        let sorted = 0
        while (++sorted < rows.length) {
          for (let i = 0; i < rows.length - sorted; i++) {
            if (!this.sort.ascending & this.resolve(rows[i], this.sort.by) < this.resolve(rows[i + 1], this.sort.by)) {
              const temp = rows[i]
              rows[i] = rows[i + 1]
              rows[i + 1] = temp
            }
            if (this.sort.ascending & this.resolve(rows[i], this.sort.by) > this.resolve(rows[i + 1], this.sort.by)) {
              const temp = rows[i]
              rows[i] = rows[i + 1]
              rows[i + 1] = temp
            }
          }
        }

        this.paginator.total = rows.length
        // perform search
        if (this.search !== undefined && this.indices !== undefined && this.indices.length > 0 && this.search.trim(' ') !== '') {
          for (let i = rows.length - 1; i >= 0; i--) {
            let valid = false
            for (const index of this.indices) {
              let data = this.resolve(rows[i], index)
              if (index === 'date') {
                data = this.$moment(data).format('MMMM D, Y')
              } else {
                data = String(data)
              }
              if (data.toLowerCase().includes(this.search.trim().toLowerCase())) {
                valid = true
                break
              }
            }
            if (!valid) {
              rows.splice(i, 1)
            }
          }
        }
        this.paginator.available = rows.length
        this.rows = rows
      },
      currentPage () {
        return this.rows.slice((this.paginator.page - 1) * this.paginator.count, this.paginator.page * this.paginator.count)
      }
    }
  }
</script>
<style scoped>
  tr > th > span {
    visibility: hidden;
    transition: opacity .2s, visibility .2s;
    opacity: 0;
  }

  tr > th > span.fa-sort-asc,
  tr > th > span.fa-sort-desc,
  tr > th:hover > span {
    visibility: visible;
    opacity: 1;
  }

  @media only screen and (max-width: 800px) {
    /* Force table to not be like tables anymore */
    table,
    thead,
    tbody,
    th,
    td,
    tr {
      display: block;
    }

    /* Hide table headers (but not display: none;, for accessibility) */
    thead tr {
      position: absolute;
      top: -9999px;
      left: -9999px;
    }

    tr { border: 1px solid #ccc; }

    td {
      /* Behave like a "row" */
      border: none;
      border-bottom: 1px solid #eee;
      position: relative;
      white-space: normal;
      text-align:left;
    }

    td:before {
      /* Now like a table header */
      position: absolute;
      /* Top/left values mimic padding */
      top: 6px;
      left: 6px;
      width: 35%;
      padding-right: 10px;
      white-space: nowrap;
      text-align:left;
      font-weight: bold;
    }

    /*
    Label the data
    */
    td:before { content: attr(data-title); }
  }

  @media only screen and (max-width: 340px) {
    td {
      padding-top: 20px !important;
    }
  }
  @media only screen and (min-width: 340px) and (max-width: 450px) {
    td {
      padding-left: 50% !important;
    }
  }
  @media only screen and (min-width: 450px) and (max-width: 580px) {
    td {
      padding-left: 35% !important;
    }
  }
  @media only screen and (min-width: 580px) and (max-width: 800px) {
    td {
      padding-left: 25% !important;
    }
  }
</style>
