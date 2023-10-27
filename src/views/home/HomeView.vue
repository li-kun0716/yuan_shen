<template>
  <div class="home-app" :style="{ '--height': viewHeight + 'px' }">
    <div class="home" @wheel.prevent ref="homeRef">
      <FirstComponent />
      <SecondComponent />
      <ThirdComponent />
      <FourthComponent />
      <FifthComponent />
      <SixthComponent />
      <SeventhComponent />
    </div>
  </div>
</template>

<script>
import FirstComponent from "@/components/home/FisrtComponent.vue";
import SecondComponent from "@/components/home/SecondComponent.vue";
import ThirdComponent from "@/components/home/ThirdComponent.vue";
import FourthComponent from "@/components/home/FourthComponent.vue";
import FifthComponent from "@/components/home/FifthComponent.vue";
import SixthComponent from "@/components/home/SixthComponent.vue";
import SeventhComponent from "@/components/home/SeventhComponent_.vue";
import {
  onBeforeUnmount,
  onMounted,
  reactive,
  toRefs,
  watch,
  ref,
} from "@vue/runtime-core";
import throttle from "lodash/throttle";

export default {
  components: {
    FirstComponent,
    SecondComponent,
    ThirdComponent,
    FourthComponent,
    FifthComponent,
    SixthComponent,
    SeventhComponent,
  },
  setup() {
    const homeRef = ref(null);

    const changePageInfo = reactive({
      viewHeight: 0,
      index: 0,
      maxIndex: 0,
    });
    const wheelHandle = (e) => {
      const index = changePageInfo.index;
      if (e.wheelDelta > 0 || e.detail > 0) {
        changePageInfo.index = index - 1 < 0 ? 0 : index - 1;
      } else {
        changePageInfo.index =
          index + 1 > changePageInfo.maxIndex
            ? changePageInfo.maxIndex
            : index + 1;
      }
      homeRef.value.children[changePageInfo.index].scrollIntoView();
    };
    const changeIndexByThrottle = throttle(wheelHandle, 300, {
      leading: true,
      trailing: false,
    });

    const resizeHandle = throttle(
      () => {
        changePageInfo.viewHeight = window.innerHeight;
        homeRef.value.children[changePageInfo.index].scrollIntoView();
      },
      500,
      { leading: false, trailing: true }
    );
    onMounted(() => {
      changePageInfo.viewHeight = window.innerHeight;
      changePageInfo.maxIndex = homeRef.value.children.length - 1;
      window.addEventListener("wheel", changeIndexByThrottle);
      window.addEventListener("resize", resizeHandle);
    });

    onBeforeUnmount(() => {
      window.removeEventListener("wheel", changeIndexByThrottle);
      window.removeEventListener("resize", resizeHandle);
    });
    return {
      ...toRefs(changePageInfo),
      homeRef,
    };
  },
};
</script>
<style lang="scss" scoped>
.home-app {
  position: relative;
  width: 100%;
  height: max(100vh, var(--height));
  box-sizing: border-box;

  .home {
    height: 100%;
    box-sizing: border-box;
    overflow-y: scroll;
    min-width: 1400px;
    &::-webkit-scrollbar {
      display: none;
    }
  }
}
</style>
