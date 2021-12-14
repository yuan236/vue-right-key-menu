<template>
  <div>
    <div>
      <div ref="menuBox" class="right-key-menu" :style="menuBoxStyle" v-show="this.show">
        <template v-for="(menu, index) in menus">
          <p style="border-bottom: 1px solid #aaa;margin: 0 5px"
             v-if="index !==0 && menus[index - 1].group !== menu.group"
          />
          <span
            @mouseenter="currentMenu = menu"
            @click="menuEvent(menu)"
            class="menu-item"
            :style="{color:menu.disable &&  menu.disable() ? '#CCC' : '#101010'}"
          >

            <span style="width: 20px;display: inline-block"
                  :style="{transform: menu.transform ? `rotate(${menu.transform}deg)` : `rotate(0deg)`}"
            >
               <slot v-bind:menu="menu">
                  <i :class="menu.icon" v-if="menu.icon"/>
               </slot>
            </span>

          <span>{{ menu.name }}</span>
          <span v-if="menu.children && menu.children.length > 0" style="float: right">></span>
        </span>
        </template>
        <div class="submenu" v-if="currentMenu && currentMenu.children && currentMenu.children.length > 0"
             :style="childrenStyle"
        >
          <template v-for="(menu, index) in currentMenu.children">
            <p style="border-bottom: 1px solid #aaa;margin: 0 5px"
               v-if="index !==0 &&  currentMenu.children[index - 1].group !== menu.group"
            />
            <span
              @click="menuEvent(menu)"
              class="menu-item"
              :style="{color:menu.disable &&  menu.disable() ? '#CCC' : '#101010'}"
            >
            <span style="width: 20px;display: inline-block"
                  :style="{transform: menu.transform ? `rotate(${menu.transform}deg)` : `rotate(0deg)`}"
            >
               <slot v-bind:menu="menu">
                  <i :class="menu.icon" v-if="menu.icon"/>
               </slot>
            </span>
            <span>{{ menu.name }}</span>
            <span v-if="menu.children && menu.children.length > 0" style="float: right">></span>
            </span>
          </template>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'RightKeyMenu',
  props: {
    menus: {
      type: Array,
      default: () => {
        return [
          { icon: 'el-icon-document-copy', name: '菜单1', group: 'default', callback: () => null, disable: () => false, children: [] }
        ]
      }
    },
    disable: {
      type: Boolean,
      default: true
    }
  },
  model: {
    prop: 'menus',
    event: 'change'
  },
  data() {
    return {
      show: false,
      menuBoxStyle: {
        top: '0',
        left: '0'
      },
      defaultMenuEvent: document.oncontextmenu,
      defaultOnmouseup: document.onmouseup,
      currentMenu: {},
      menuWidth: 260,
      childrenStyle: {}
    }
  },
  computed: {
    boxSize() {
      return {
        width: 260,
        height: this.menus.length * 30
      }
    }
  },
  methods: {
    menuEvent(menu) {
      if ((menu.disable && menu.disable()) || !menu.callback) {
        return
      }
      menu.callback()
    },
    showMenu(event) {
      const windowHeight = document.body.clientHeight
      const windowWidth = document.body.clientWidth
      const set = new Set(this.menus.map(x => x.group))
      this.menuBoxStyle = {
        left: (windowWidth - event.clientX >= this.boxSize.width ? event.clientX : event.clientX - this.boxSize.width) + 'px',
        top: (windowHeight - event.clientY >= this.boxSize.height ? event.clientY : event.clientY - this.boxSize.height) + 'px',
        height: this.boxSize.height + set.size + 1 + 'px',
        width: this.boxSize.width + 'px'
      }
      this.show = false
      this.currentMenu = {}
      setTimeout(() => {
        this.show = true
      }, 10)
    },
    showSubMenu() {
      const windowHeight = document.body.clientHeight
      const windowWidth = document.body.clientWidth
      const boxLeft = this.$refs.menuBox.offsetLeft
      const boxTop = this.$refs.menuBox.offsetTop
      const subIndex = this.menus.indexOf(this.currentMenu)
      const defaultTop = subIndex * 30 + boxTop + new Set(this.menus.slice(0, subIndex).map(x => x.group)).size + 1
      const defaultLeft = boxLeft + this.boxSize.width - 10
      const subSet = new Set(this.currentMenu.children.map(x => x.group))
      const height = this.currentMenu.children.length * 30 + subSet.size + 1
      this.childrenStyle = {
        left: (defaultLeft + this.boxSize.width - 10 <= windowWidth ? defaultLeft : boxLeft - this.boxSize.width + 10) + 'px',
        top: (defaultTop + height > windowHeight ? windowHeight - height : defaultTop) + 'px',
        height: this.currentMenu.children.length * 30 + 'px',
        width: 260 + 'px'
      }
    },
    initDocumentEvent() {
      if (this.disable) {
        document.onmouseup = this.defaultOnmouseup
        document.oncontextmenu = this.defaultMenuEvent
        return
      }
      document.onmouseup = ($event) => {
        this.show = false
        if ($event.button === 2) {
          document.oncontextmenu = () => false
          this.showMenu($event)
        }
      }
    }
  },
  mounted() {
    this.initDocumentEvent()
  },
  watch: {
    disable() {
      this.initDocumentEvent()
    },
    currentMenu: {
      handler() {
        if (this.currentMenu.children && this.currentMenu.children.length > 0) {
          this.showSubMenu()
        }
      }
    }
  }
}
</script>

<style scoped>
.right-key-menu {
  width: 260px;
  position: fixed;
  z-index: 1000;
  box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04);
  border: 1px solid #ccc;
  background-color: #EEE;
  color: #101010;
  font-size: 12px;
}

.right-key-menu, .submenu {
  position: fixed;
  height: 50px;
  z-index: 1001;
  color: #101010;
  font-size: 12px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04);
  border: 1px solid #ccc;
  background-color: #EEE;
}

.right-key-menu .menu-item {
  line-height: 30px;
  display: block;
  padding: 0 10px;
  cursor: pointer;
}

.right-key-menu .menu-item:hover {
  background-color: #ddd;
}
</style>
