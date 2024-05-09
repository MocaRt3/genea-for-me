<template>
  <div class="container">
    <div class="row" style="margin-bottom: 0">
      <div class="col s12 offset-m1">
        <h1 class="header">Personal Details</h1>
      </div>
    </div>
    <personaldetails-list
      :items="person.items"
      :mode="'simple'"
      :level="0"></personaldetails-list>
    <div class="row">
      <div class="col s12 offset-m1">
        <ul class="collection">
          <li class="collection-item">
            <div class="switch">
              <label>
                <input type="checkbox" v-model="advancedMode" />
                <span class="lever"></span>
                Advanced fields
              </label>
            </div>
          </li>
        </ul>
      </div>
    </div>
    <personaldetails-list
      :items="person.items"
      :mode="'advanced'"
      :level="0"
      v-if="advancedMode"></personaldetails-list>
    <!-- Modals -->
    <div id="modalEditPersonalDetail" class="modal">
      <div class="modal-content">
        <h4>Edit</h4>
        <div class="input-field">
          <label for="editTag" :class="{ active: modalEditPersonalDetail.tag }">
            Tag
          </label>
          <input
            id="editTag"
            type="text"
            v-model="modalEditPersonalDetail.tag" />
        </div>
        <div class="input-field">
          <label for="editValue" class="active">Value</label>
          <textarea
            id="editValue"
            class="materialize-textarea"
            v-model="modalEditPersonalDetail.value"></textarea>
        </div>
      </div>
      <div class="modal-footer">
        <a class="modal-close waves-effect waves-green btn-flat">Close</a>
      </div>
    </div>
    <div id="modalAddPersonalDetail" class="modal">
      <div class="modal-content">
        <h4>Add</h4>
        <ul class="collection with-header">
          <li class="collection-header"><h5>Foundation</h5></li>
          <li class="collection-item modal-close" v-on:click="addItem('NAME')">
            Name
            <a class="secondary-content"><i class="material-icons">add</i></a>
          </li>
          <li class="collection-item modal-close" v-on:click="addItem('BIRTH')">
            Birth
            <a class="secondary-content"><i class="material-icons">add</i></a>
          </li>
          <li class="collection-item modal-close" v-on:click="addItem('DEATH')">
            Death
            <a class="secondary-content"><i class="material-icons">add</i></a>
          </li>
          <li
            class="collection-item modal-close"
            v-on:click="addItem('BURIAL')">
            Burial
            <a class="secondary-content"><i class="material-icons">add</i></a>
          </li>
          <li class="collection-header"><h5>Annotation</h5></li>
          <li class="collection-item modal-close" v-on:click="addItem('NOTES')">
            Notes
            <a class="secondary-content"><i class="material-icons">add</i></a>
          </li>
          <li class="collection-item modal-close" v-on:click="addItem('TEXT')">
            Text
            <a class="secondary-content"><i class="material-icons">add</i></a>
          </li>
          <li class="collection-header"><h5>Custom</h5></li>
          <li class="collection-item modal-close" v-on:click="addItem('_UID')">
            UUID
            <a class="secondary-content"><i class="material-icons">add</i></a>
          </li>
          <li
            class="collection-item modal-close"
            v-on:click="addItem('CUSTOM')">
            Custom
            <a class="secondary-content"><i class="material-icons">add</i></a>
          </li>
        </ul>
      </div>
      <div class="modal-footer">
        <a class="modal-close waves-effect waves-green btn-flat">Close</a>
      </div>
    </div>
    <div style="position: fixed; bottom: 45px; right: 24px">
      <button
        data-target="modalAddPersonalDetail"
        class="btn modal-trigger btn-floating btn-large waves-effect waves-light red">
        <i class="material-icons">add</i>
      </button>
    </div>
  </div>
</template>

<script>
// import personaldetails_list from "./personaldetails_list.vue";

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
  props: ["person"],
  data: function () {
    return {
      advancedModeState: false,
      modalEditPersonalDetail: {},
    };
  },
  components: {
    // "personaldetails-list": personaldetails_list,
    personaldetails_list: Vue.defineAsyncComponent(() =>
      loadModule("./personaldetails_list.vue", options)
    ),
  },
  computed: {
    advancedMode: {
      get() {
        return this.advancedModeState;
      },
      set(state) {
        if (state == true) {
          this.advancedModeState = true;
          window.setTimeout(function () {
            M.AutoInit();
          }, 200);
        } else {
          this.advancedModeState = false;
        }
      },
    },
  },
  methods: {
    addItem: function (item) {
      if (item == "NAME") {
        this.person.items.push({
          level: 1,
          tag: "NAME",
          value: "",
          items: [
            { level: 2, tag: "GIVN", value: "" },
            { level: 2, tag: "SURN", value: "" },
          ],
        });
      } else if (item == "BIRTH") {
        this.person.items.push({
          level: 1,
          tag: "BIRT",
          value: "",
          items: [
            { level: 2, tag: "DATE", value: "" },
            { level: 2, tag: "PLAC", value: "" },
          ],
        });
      } else if (item == "DEATH") {
        this.person.items.push({
          level: 1,
          tag: "DEAT",
          value: "",
          items: [
            { level: 2, tag: "DATE", value: "" },
            { level: 2, tag: "PLAC", value: "" },
          ],
        });
      } else if (item == "BURIAL") {
        this.person.items.push({
          level: 1,
          tag: "BURI",
          value: "",
          items: [
            { level: 2, tag: "DATE", value: "" },
            { level: 2, tag: "PLAC", value: "" },
          ],
        });
      } else if (item == "NOTES") {
        this.person.items.push({ level: 1, tag: "NOTE", value: "", items: [] });
      } else if (item == "TEXT") {
        this.person.items.push({ level: 1, tag: "TEXT", value: "", items: [] });
      } else if (item == "_UID") {
        this.person.items.push({
          level: 1,
          tag: "_UID",
          value: crypto.randomUUID(),
          items: [],
        });
      } else if (item == "CUSTOM") {
        this.advancedMode = true;
        this.person.items.push({ level: 1, tag: "", value: "", items: [] });
        var rootField = this.$children[0];
        window.setTimeout(function () {
          rootField.$children[rootField.$children.length - 1].editItem();
        }, 200);
      }
    },
  },
};
</script>
