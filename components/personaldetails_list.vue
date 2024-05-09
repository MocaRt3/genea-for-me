<template>
  <div class="row">
    <template v-for="(detail, index) in items">
      <personaldetails-item
        :detail="detail"
        :index="index"
        :mode="mode"
        :level="level"></personaldetails-item>
    </template>
  </div>
</template>

<script>
// import personaldetails_item from "./personaldetails_item.vue";

const options = {
  moduleCache: {
    vue: Vue,
  },
  async getFile(url) {
    const res = await fetch(url);
    if (!res.ok)
      throw Object.assign(new Error(res.statusText + " " + url), { res });
    return {
      getContentData: asBinary => (asBinary ? res.arrayBuffer() : res.text()),
    };
  },
  addStyle(textContent) {
    const style = Object.assign(document.createElement("style"), {
      textContent,
    });
    const ref = document.head.getElementsByTagName("style")[0] || null;
    document.head.insertBefore(style, ref);
  },
};

const { loadModule } = window["vue3-sfc-loader"];

export default {
  props: ["items", "mode", "level"],
  components: {
    // "personaldetails-item": personaldetails_item,
    personaldetails_item: Vue.defineAsyncComponent(() =>
      loadModule("./Personaldetails_item.vue", options)
    ),
  },
};
</script>
