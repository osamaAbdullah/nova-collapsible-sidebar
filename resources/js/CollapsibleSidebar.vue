<template>
  <div class="sidebar"
       :class="sidebarClass"
       ref="sidebar"
       @keyup.esc="toggleCollapsed">
    <div class="sidebar-content">
      <slot/>
    </div>
  </div>
</template>
<style lang="scss">
.sidebar-expanded {
  & + .content {
    max-width: calc(100% - 13.75rem);
    margin-left: 13.75rem;
  }
}

.sidebar-collapsed {
  &.collapsed {
    width: 1rem;
    overflow-y: hidden;
  }

  & + .content {
    max-width: 100%;
    padding-left: 1rem;
  }
}

.sidebar-content {
  padding: 5.7rem 1.5rem 0 1.5rem;
  width: 10.75rem;

  ul li a {
    white-space: nowrap;
  }
}

.sidebar {
  width: 13.75rem;
  position: fixed;
  top: 0;
  left: 0;
  overflow-x: hidden;
  z-index: 90;
  min-height: 100vh;
  max-height: 100vh;
  transition: width .3s;

  &::-webkit-scrollbar {
    width: 0.5rem;
  }

  &::-webkit-scrollbar-track {
    background-color: transparent;
  }

  &::-webkit-scrollbar-thumb {
    background-color: #9c9c9c;
    border-radius: 100vh;
  }

  &::-webkit-scrollbar-thumb:hover {
    background-color: #7d7c7c;
  }

  @supports (scrollbar-color: #9c9c9c transparent) {
    * {
      scrollbar-color: #9c9c9c transparent;
      scrollbar-width: thin;
    }
  }
}
</style>
<script>
export default {
  props: {
    persist: {
      type: Boolean,
    },
    storageKey: {
      type: String, default: 'nova-navigation-collapsed',
    },
    toggleKeyCode: {
      type: String, default: 'Escape',
    },
  },
  beforeMount() {

    if (!this.persist) {
      localStorage.removeItem(this.storageKey)
    } else {
      if (localStorage.getItem(this.storageKey) === 'sidebar-collapsed collapsed') {
        this.sidebarClass = 'sidebar-collapsed collapsed';
      } else {
        this.sidebarClass = 'sidebar-expanded';
      }
    }

    window.addEventListener('keyup', event => {
      if (event.code === this.toggleKeyCode) {
        this.toggleCollapsed();
      }
    });
  },
  mounted() {
    this.sidebarClass === 'sidebar-collapsed collapsed' && this.collapse();
  },
  data: () => {
    return {
      sidebarClass: 'sidebar-expanded',
    };
  },
  methods: {
    collapse() {
      this.sidebarClass = 'sidebar-collapsed collapsed';
      this.$refs['sidebar'].onmouseover = this.expandOnHover;
      this.$refs['sidebar'].onmouseleave = this.collapseOnLeave;
      if (this.persist) {
        localStorage.setItem(this.storageKey, 'sidebar-collapsed collapsed');
      }
    },
    expand() {
      this.sidebarClass = 'sidebar-expanded';
      this.$refs['sidebar'].onmouseover = null;
      this.$refs['sidebar'].mouseleave = null;
      if (this.persist) {
        localStorage.setItem(this.storageKey, 'sidebar-expanded');
      }
    },
    toggleCollapsed() {
      this.sidebarClass === 'sidebar-expanded' ?
          this.collapse() :
          this.expand();
    },
    expandOnHover() {
      if (this.$refs['sidebar'].classList.contains('collapsed')) {
        this.$refs['sidebar'].classList.remove('collapsed')
      }
    },
    collapseOnLeave() {
      if (!this.$refs['sidebar'].classList.contains('collapsed')) {
        this.$refs['sidebar'].classList.add("collapsed")
      }
    },
  },
};
</script>