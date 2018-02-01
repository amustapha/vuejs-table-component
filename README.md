# vuejs-table-component

[![npm](https://img.shields.io/npm/v/vuejs-table-component.svg)](https://www.npmjs.com/package/vuejs-table-component)
[![npm](https://img.shields.io/npm/dt/vuejs-table-component.svg)](https://www.npmjs.com/package/vuejs-table-component)
[![npm](https://img.shields.io/npm/dm/vuejs-table-component.svg)](https://www.npmjs.com/package/vuejs-table-component)
[![Build Status](https://travis-ci.org/kevinongko/vuejs-table-component.svg?branch=master)](https://travis-ci.org/kevinongko/vuejs-table-component)
[![Codecov](https://img.shields.io/codecov/c/github/kevinongko/vuejs-table-component.svg)](https://codecov.io/gh/kevinongko/vuejs-table-component)
[![npm](https://img.shields.io/npm/l/vuejs-table-component.svg)](http://opensource.org/licenses/MIT)

A vue component for rendering datatables. With customizable sorting and searching capabilities.

[Live Demo](https://kevinongko.github.io/vuejs-table-component/)

**Works with Vue 2.***

## Installation

### Install via CDN
```html
<script src="https://unpkg.com/accounting-js"></script>
<script src="https://unpkg.com/vue"></script>
<script src="https://unpkg.com/vuejs-table-component"></script>

<script>
  Vue.use(Datatable.default)
</script>
```
### Install via NPM
```sh
$ npm install vuejs-table-component --save
```

#### Register as Component
```js
import Vue from 'vue'
import Datatable from 'vuejs-table-component'

export default {
  name: 'App',

  components: {
    Datatable
  }
}
```

#### Register as Plugin
```js
import Vue from 'vue'
import Datatable from 'vuejs-table-component'

Vue.use(Datatable)
```

## Usage


### Quick example

```vue
<template>
  <datatable columns="columns" dataset="rows"></datatable>
</template>

<script>
import Datatable from 'vuejs-table-component'

export default {
  name: 'App',

  components: {
    Datatable
  },

  data: () => ({
    price: ''
  }),
}
</script>
```

## Props
|Props|Description|Required|Type|Default|
|-----|-----------|--------|----|-------|
|columns|Corresponding columns of the table|true|Array|-|
|dataset|Data to be used for pupulating the table|true|Array|-|
|perpage|List of page sizes|false|Array|[10, 50, 100]|
|indices|Fields that should be searchable by search|false|Array|[]|
|search|A v-model value binded with a search field| false|String|''|

## License
