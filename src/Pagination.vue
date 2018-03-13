<template>
  <div class="">
    <div class="row">
      <p class="col">
        <span>Showing {{ (paginator.count * (paginator.page - 1)) + 1 }} to {{ Math.min(paginator.count * paginator.page, paginator.available) }} of {{ paginator.available }} entries.
          <span v-if="paginator.available < paginator.total">
            Filtered from {{ paginator.total }} entries
          </span>
        </span>
        <select v-model="paginator.count" class="form-control custom-select" style="width: 64px; height: 23px; padding-top: 0 ">
          <option>50</option>
          <option>100</option>
          <option>250</option>
        </select>
      </p>
      <nav
        aria-label="Page navigation"
        class="col">
        <ul
          class="pagination justify-content-end pull-right pagination-sm"
          style="margin: 0;">
          <li
            class="page-item"
            :class="[paginator.page == 1 ? 'disabled' : '']">
            <a
              class="page-link"
              href="#"
              tabindex="-1"
              @click=" paginator.page = paginator.page > 1 ? paginator.page - 1 : 1">Previous</a>
          </li>
          <li
            :class="[paginator.page == page ? 'active' : '']"
            class="page-item"
            v-for="page in paginationSequence()"
            :key="page">
            <a
              class="page-link"
              @click.prevent="paginator.page = page"
              v-if="page=='...'">{{ page }}</a>
            <a
              class="page-link"
              href="#"
              @click.prevent="paginator.page = page"
              v-else>{{ page }}</a>
          </li>

          <li
            class="page-item"
            :class="[paginator.page == Math.ceil(paginator.available / paginator.count) ? 'disabled' : '']">
            <a
              class="page-link"
              href="#"
              @click.prevent=" paginator.page = paginator.page < Math.ceil(paginator.available / paginator.count) ? paginator.page + 1 : Math.ceil(paginator.available / paginator.count)">Next</a>
          </li>
        </ul>
      </nav>
    </div>
  </div>
</template>
<script>
  export default {
    props: {
      paginator: {
        type: Object,
        required: true
      }
    },
    methods: {
      paginationSequence () {
        const maxPage = Math.ceil(this.paginator.available / this.paginator.count)
        let pages = []
        if (maxPage < 5) {
          pages = Array.from(Array(maxPage), (val, index) => index + 1)
        } else {
          pages = Array.from(Array(2), (val, index) => index + 1)
          if (this.paginator.page > 4) {
            pages.push('...')
          }
          if (maxPage - this.paginator.page > 0) {
            pages.push.apply(pages, Array.from(Array(Math.min(maxPage - this.paginator.page, 3)), (val, index) => index + Math.max(3, this.paginator.page - 1)))
          }
          if (this.paginator.page < maxPage - 3) {
            pages.push('...')
          }
          pages.push.apply(pages, Array.from(Array(2), (val, index) => index + maxPage - 1))
        }
        return pages
      }
    }
  }
</script>
<style>
</style>
