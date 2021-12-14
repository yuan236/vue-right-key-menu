# vue-right-key-menu
用于 vue 的右键菜单
#### 使用说明
- 直接下载或者复制文件丢进项目里面用吧。
- 仅支持二级菜单。三级以上那么复杂的，要写递归的，我那么懒。肯定是不可能写的（在下绝对不是不会写，绝对！哼~）
#### 使用示例(直接在vue文件中导入，然后项下面这样写在你的组件里就可以，别忘了 import 哟)
```html
<RightKeyMenu v-model="rightKeyMenu" :disable="disableRightKeyMenu">
   <template v-slot="menu">
      <!-- menu 为 v-model 项对象 -->
      <i :class="menu.icon" v-if="menu.icon"/>
   </template>
</RightKeyMenu>
```
#### prop 说明
```
disable: 禁用并且执行浏览器默认右键菜单功能
```
#### v-model 对象说明（在代码第65行有一个示例）
- icon：字符串型，当然是图标了，不过这里使用的是 element-ui 的图标标签，其它 ui 框架的小伙伴也不用担心，使用默认插槽，会返回改菜单项，把你的图标标签通过插槽替换掉原来的就可以了。如上例中注释所指部分。
- name：String 型，当然是菜单的名称了。
- group：String 型，分组，这个分组比较惨淡，就是如果上一条菜单的分组跟这一条不一样的话，就会在这一条上面画一条分割线，仅此而已。
- callback：Function 型，传入一个函数，如代码中65行，回调函数，就是点击这个菜单执行什么，相比起抛出时间写一大堆 v-on ，当然是回调函数来得更高端一些了。不用返回值，反正你返回了我也不接收~哼~
- disable：Function 型，字面意思，禁用该项。传入一个函数，函数返回 Boolean，为 true 禁用该项，点击也不会调用回调。为 false 正常。默认false
- transform： Number 型，图标旋转，有些图标需要整整齐齐，有的就喜欢歪歪扭扭。就像你写撸码时的坐姿。
- children： Array 型，数组里就是一个普通菜单项，拥有以上的所有字段，除了 name 每一项都可以不填，说了只有二级菜单的哈，所以如果子菜单再有子菜单就不生效了。就此打住

**没有了，一滴都没有了**
