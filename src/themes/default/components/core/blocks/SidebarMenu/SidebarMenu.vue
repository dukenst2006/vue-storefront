<template>
    <div class="sidebar-menu bg-lightgray position-fixed top-0" :class="{ active: isOpen }">
        <div class="row">
            <div class="col-md-12 close bg-white align-right display-inline-flex end-xs" @click="closeMenu">
                <i class="material-icons p15">close</i>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <ul>
                    <!-- TO-DO: Remove closemenu and handle it via store -->
                    <li @click="closeMenu">
                        <router-link to="/" exact>Home</router-link>
                    </li>
                    <li @click="closeMenu" v-for='category in categories'>
                        <router-link :to="{ name: 'category', params: { id: category.id, slug: category.slug }}">{{ category.name }}</router-link>
                        <ul v-if="category.children_data">
                            <li @click="closeMenu" v-for='subcat in category.children_data'>
                                <router-link :to="{ name: 'category', params: { id: subcat.id, slug: subcat.slug }}">{{ subcat.name }}</router-link>
                            </li>
                            
                        </ul>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
import { coreComponent } from 'lib/themes'

import EventBus from 'src/event-bus/event-bus'

export default {
  data () {
    return {
      isOpen: false
    }
  },
  computed: {
    categories () {
      return this.$store.state.catalog.categories.filter((op) => {
        return op.level === 2 // display only the root level (level =1 => Default Category)
      })
    }
  },
  created () {
    const self = this
    EventBus.$on('toggle-sidebar-menu', () => {
      self.isOpen = !self.isOpen
    })
    this.$store.dispatch('catalog/loadCategories', {})
  },
  methods: {
    closeMenu () {
      this.isOpen = false
      EventBus.$emit('toggle-overlay')
    }
  },
  mixins: [coreComponent('core/blocks/SidebarMenu/SidebarMenu')]
}
</script>

<style scoped>
.sidebar-menu {
    height: 100vh;
    width: 350px;
    left: -350px;
    overflow: hidden;
}
.sidebar-menu.active {
    left: 0;
}
.close {
    cursor: pointer;
}
</style>
