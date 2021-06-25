<template>
  <sunburst :data="normalizedIssues">

  <!-- Add behaviors -->
  <template slot-scope="{ on, actions }">
    <highlightOnHover v-bind="{ on, actions }"/>
    <zoomOnClick v-bind="{ on, actions }"/>
  </template>

  <!-- Add information to be displayed on top the graph -->
  <nodeInfoDisplayer slot="top" slot-scope="{ nodes }" :current="nodes.mouseOver" :root="nodes.root" description="of visits begin with this sequence of pages" />

  <!-- Add bottom legend -->
  <breadcrumbTrail slot="legend" slot-scope="{ nodes, colorGetter, width }" :current="nodes.mouseOver" :root="nodes.root" :colorGetter="colorGetter" :from="nodes.clicked" :width="width" />

  </sunburst>
</template>

<script>
import {
  breadcrumbTrail,
  highlightOnHover,
  nodeInfoDisplayer,
  sunburst,
  zoomOnClick
} from 'vue-d3-sunburst'
import _ from 'lodash'
import 'vue-d3-sunburst/dist/vue-d3-sunburst.css'

export default {
  components: {
    breadcrumbTrail,
    highlightOnHover,
    nodeInfoDisplayer,
    sunburst,
    zoomOnClick
  },
  props: ['issues'],
  computed: {
    normalizedIssues: function () {
      const children = []
      _.each(this.issues, (issue) => {
        children.push({
          'name': issue.day,
          'size': issue.issues
        })
      })

      return {
        "name": "flare",
        "children": children
      }
    }
  }
}
</script>