<template>
  <div class="emoji">
    <h1>This is Emoji-Picker</h1>
    <slot
      name="invoker"
      :events="{ click: (e:MouseEvent) => toggle(e) }"
    ></slot>
    <div v-click-outside="hide">
      <slot
        name="picker"
        :emojis="Emojis"
        :insert="insert"
        :display="display"
      ></slot>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted, onUnmounted, ref } from "vue";
import type { PropType } from "vue";
import Emojis from "@/utils/emojis";

type Data = {
  display: {
    x: number;
    y: number;
    visible: boolean;
  };
};
type ClickOutsideElement = HTMLElement & {
  __vueClickOutside__: ((e: MouseEvent) => void) | null;
};

//生命周期
onMounted(() => {
  document.addEventListener("keyup", escape);
});
onUnmounted(() => {
  document.removeEventListener("keyup", escape);
});

//定义计算属性
const emojis: any = computed(() => {
  if (props.search) {
    const obj: Record<string, Record<string, string>> = {};

    for (const category in props.emojiTable) {
      obj[category] = {};

      for (const emoji in props.emojiTable[category]) {
        if (new RegExp(`.*${escapeRegExp(props.search)}.*`).test(emoji)) {
          obj[category][emoji] = props.emojiTable[category][emoji];
        }
      }

      if (Object.keys(obj[category]).length === 0) {
        delete obj[category];
      }
    }

    return obj;
  }

  return props.emojiTable;
});

//定义props
const props = defineProps({
  search: {
    type: String as PropType<string>,
    required: false,
    default: "",
  },
  emojiTable: {
    type: Object as PropType<Record<string, Record<string, string>>>,
    required: false,
    // default() {
    //   return emojis;
    // },
  },
});

//定义emits
const emit = defineEmits(["emoji", "submit"]);

//定义数据
const display = ref({
  x: 0,
  y: 0,
  visible: false,
});

//定义方法
// https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions#escaping
const escapeRegExp = (s: string) => s.replace(/[.*+?^${}()|[\]\\]/g, "\\$&");

function insert(emoji: string): void {
  emit("emoji", emoji);
}
function toggle(e: MouseEvent): void {
  display.value.visible = !display.value.visible;
  display.value.x = e.clientX;
  display.value.y = e.clientY;
}
function hide(): void {
  display.value.visible = false;
}
function escape(e: KeyboardEvent): void {
  if (display.value.visible === true && e.keyCode === 27) {
    display.value.visible = false;
  }
}

//自定义指令
const vClickOutside = {
  bind(el: ClickOutsideElement, binding: any) {
    if (typeof binding.value !== "function") {
      return;
    }

    const bubble = binding.modifiers.bubble;
    const handler = (e: any) => {
      if (bubble || (!el.contains(e.target) && el !== e.target)) {
        binding.value(e);
      }
    };
    el.__vueClickOutside__ = handler;

    document.addEventListener("click", handler);
  },
  unbind(el: ClickOutsideElement) {
    if (el.__vueClickOutside__ !== null) {
      document.removeEventListener("click", el.__vueClickOutside__);

      el.__vueClickOutside__ = null;
    }
  },
};
</script>

<style>
.emoji {
  height: 200px;
}
</style>
