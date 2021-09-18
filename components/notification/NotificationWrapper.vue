<template>
<vue-notification-renderless>
  <div class="flash--wrapper" slot-scope="{ items }">
    <transition-group name="flash">
    <notification-item
        v-for="item in items"
        :item="item"
        :key="item.id"
      ></notification-item>
    </transition-group>
  </div>
</vue-notification-renderless>
</template>


<style scoped>
/* https://codesandbox.io/s/88v5v46xl9?file=/src/components/FlashWrapper.vue:537-672 */
.flash--wrapper {
  position: fixed;
  top: 0;
  right: 0;
  width: 100%;
  z-index: 1;
}

.flash-enter-active,
.flash-leave-active {
  transition: all 0.5s;
}

.flash-enter,
.flash-leave-to {
  opacity: 0;
  transform: translateX(60px);
}

.flash-move {
  transition: transform 0.2s;
}
</style>
