<template>
  <div class="sidebar"
       :class="{collapsed}"
       :style="sidebarStyle"
       @keyup.esc="toggleCollapsed"
       @mouseover="expandOnHover"
       @mouseleave="collapseOnLeave">
    <slot/>
  </div>
</template>

<style lang="scss">
  .sidebar {
    position: relative;
    transition: 0.3s;

    &.collapsed {
      > *:not(.trigger) {
        display: none;
      }

      .trigger + a {
        display: block;
      }

      + .content {
        max-width: inherit;
      }
    }
  }
</style>

<script>

  export default {

    props: {
      height: {
        default: '60px',
      },
      width: {
        default: '60px',
      },
      persist: {
        type: Boolean,
      },
      storageKey: {
        type: String,
        default: 'nova-navigation-collapsed',
      },
      toggleKeyCode: {
        type: String,
        default: 'Escape',
      },
    },

    mounted() {

      if (!this.persist) {
        localStorage.removeItem(this.storageKey)
      } else {
        this.collapsed = localStorage.getItem(this.storageKey) == 1;
      }

      window.addEventListener('keyup', event => {
        if (event.code === this.toggleKeyCode) {
          this.toggleCollapsed();
        }
      });
    },
    data: () => {
      return {
        marginLeft: -210,
        collapsed: false,
        expandedByHover: false,
      };
    },
    computed: {
      sidebarStyle() {
        return {
          marginLeft: (this.collapsed ? this.marginLeft : 0) + 'px',
        };
      },
    },
    methods: {
      toggleCollapsed() {
        this.collapsed = !this.collapsed;

        if (this.persist) {
          localStorage.setItem(this.storageKey, this.collapsed ? 1 : 0);
        }
      },
        expandOnHover() {
          if (this.collapsed) {
              this.collapsed = false;
              this.expandedByHover = true;
          }
      },
        collapseOnLeave() {
          if (this.expandedByHover) {
              this.collapsed = true;
              this.expandedByHover = false;
          }
      },
    },
  };
</script>
