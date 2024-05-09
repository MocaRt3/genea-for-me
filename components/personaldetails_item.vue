<template>
  <div class="row" style="padding: 0; margin: 0">
    <div
      v-if="mode == 'advanced'"
      class="col offset-s1"
      :class="{ s7: level == 0, s11: level != 0 }">
      <div class="input-field">
        <label :for="id" :class="{ active: detail.value }">
          {{ detail.tag }} ({{ tag(detail.tag).name }})
        </label>
        <input :id="id" type="text" v-model="detail.value" />
        <div
          class="fixed-action-btn horizontal direction-top direction-left"
          style="position: absolute; display: inline-block; right: 24px">
          <a
            class="btn-floating btn-large transparent z-depth-0"
            style="cursor: default">
            <i class="large material-icons teal-text text-lighten-3">
              more_vert
            </i>
          </a>
          <ul>
            <li v-if="index > 0">
              <a class="btn-floating teal lighten-4">
                <i class="material-icons" v-on:click="upItem()">
                  arrow_drop_up
                </i>
              </a>
            </li>
            <li v-if="index < $parent.items.length - 1">
              <a class="btn-floating teal lighten-4">
                <i class="material-icons" v-on:click="downItem()">
                  arrow_drop_down
                </i>
              </a>
            </li>
            <li>
              <a class="btn-floating red">
                <i class="material-icons" v-on:click="deleteItem()">clear</i>
              </a>
            </li>
            <li>
              <a class="btn-floating yellow darken-1">
                <i class="material-icons" v-on:click="addItem()">add</i>
              </a>
            </li>
            <li>
              <a class="btn-floating green">
                <i class="material-icons" v-on:click="editItem()">mode_edit</i>
              </a>
            </li>
          </ul>
        </div>
      </div>
      <personaldetails-list
        :items="detail.items"
        :mode="mode"
        :level="level + 1"></personaldetails-list>
    </div>

    <div v-else-if="tag(detail.tag).visible == true" class="col s12 offset-m1">
      <template v-if="detail.tag == 'OBJE'">
        <div class="card">
          <div
            class="card-image"
            v-if="
              (
                (detail.items.find(item => item.tag == 'FILE') || {}).value ||
                ''
              ).startsWith('http')
            ">
            <img
              :src="
                (detail.items.find(item => item.tag == 'FILE') || {}).value
              " />
            <span class="card-title">
              {{ (detail.items.find(item => item.tag == "_DATE") || {}).value }}
            </span>
          </div>
          <div class="card-content">
            {{ (detail.items.find(item => item.tag == "TITL") || {}).value }}
          </div>
        </div>
      </template>

      <template v-else-if="detail.tag == 'NAME'">
        <div class="col s6">
          <div class="input-field">
            <label :for="id + '1'" class="active">{{ tag("GIVN").name }}</label>
            <input
              :id="id + '1'"
              type="text"
              v-model="
                (
                  detail.items[detail.items.findIndex(x => x.tag == 'GIVN')] ||
                  {}
                ).value
              "
              v-on:input="
                detail.value =
                  detail.items[detail.items.findIndex(x => x.tag == 'GIVN')]
                    .value +
                  ' /' +
                  detail.items[detail.items.findIndex(x => x.tag == 'SURN')]
                    .value +
                  '/'
              " />
          </div>
        </div>
        <div class="col s6">
          <div class="input-field">
            <label :for="id + '2'" class="active">{{ tag("SURN").name }}</label>
            <input
              :id="id + '2'"
              type="text"
              v-model="
                (
                  detail.items[detail.items.findIndex(x => x.tag == 'SURN')] ||
                  {}
                ).value
              "
              v-on:input="
                detail.value =
                  detail.items[detail.items.findIndex(x => x.tag == 'GIVN')]
                    .value +
                  ' /' +
                  detail.items[detail.items.findIndex(x => x.tag == 'SURN')]
                    .value +
                  '/'
              " />
          </div>
        </div>
      </template>

      <template v-else-if="detail.tag == 'BIRT'">
        <div class="col s6">
          <div class="input-field">
            <label :for="id + '1'" class="active">Date of Birth</label>
            <input
              :id="id + '1'"
              type="text"
              v-model="
                (
                  detail.items[detail.items.findIndex(x => x.tag == 'DATE')] ||
                  {}
                ).value
              " />
          </div>
        </div>
        <div class="col s6">
          <div class="input-field">
            <label :for="id + '2'" class="active">Place of Birth</label>
            <input
              :id="id + '2'"
              type="text"
              v-model="
                (
                  detail.items[detail.items.findIndex(x => x.tag == 'PLAC')] ||
                  {}
                ).value
              " />
          </div>
        </div>
      </template>

      <template v-else-if="detail.tag == 'DEAT'">
        <div class="col s6">
          <div class="input-field">
            <label :for="id + '1'" class="active">Date of Death</label>
            <input
              :id="id + '1'"
              type="text"
              v-model="
                (
                  detail.items[detail.items.findIndex(x => x.tag == 'DATE')] ||
                  {}
                ).value
              " />
          </div>
        </div>
        <div class="col s6">
          <div class="input-field">
            <label :for="id + '2'" class="active">Place of Death</label>
            <input
              :id="id + '2'"
              type="text"
              v-model="
                (
                  detail.items[detail.items.findIndex(x => x.tag == 'PLAC')] ||
                  {}
                ).value
              " />
          </div>
        </div>
      </template>

      <template v-else-if="detail.tag == 'BURI'">
        <div class="col s6">
          <div class="input-field">
            <label :for="id" class="active">Place of Burial</label>
            <input
              :id="id"
              type="text"
              v-model="
                (
                  detail.items[detail.items.findIndex(x => x.tag == 'PLAC')] ||
                  {}
                ).value
              " />
          </div>
        </div>
      </template>

      <template v-else-if="detail.tag == '_UID'">
        <div class="col s6">
          <div class="input-field">
            <label :for="id" class="active">UUID</label>
            <input :id="id" type="text" v-model="detail.value" />
          </div>
        </div>
      </template>

      <template v-else-if="tag(detail.tag).field == 'textarea'">
        <label :for="id" :class="{ active: detail.value }">
          {{ tag(detail.tag).name }}
        </label>
        <blockquote class="teal lighten-5">
          <span v-html="parseHtml(detail.value)"></span>
        </blockquote>
        <textarea
          :id="id"
          class="materialize-textarea"
          v-model="detail.value"
          v-if="edit"></textarea>
        <div class="right-align">
          <a
            class="waves-effect waves-light btn"
            v-if="!edit"
            v-on:click="
              edit = true;
              focus(id);
            ">
            <i class="material-icons left">edit</i>
            Edit
          </a>
          <a
            class="waves-effect waves-light btn"
            v-if="edit"
            v-on:click="edit = false">
            <i class="material-icons left">vertical_align_top</i>
            Collapse
          </a>
        </div>
      </template>

      <template v-else>
        <div class="col s12">
          <div class="input-field">
            <label :for="id" :class="{ active: detail.value }">
              {{ tag(detail.tag).name }}
            </label>
            <input :id="id" type="text" v-model="detail.value" />
          </div>
        </div>
        <personaldetails-list
          :items="detail.items"
          :mode="mode"
          :level="level + 1"
          v-if="!tag(detail.tag).collapseChildren"></personaldetails-list>
      </template>
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
  props: ["detail", "index", "mode", "level"],
  components: {
    // "personaldetails-list": personaldetails_list,
    personaldetails_list: Vue.defineAsyncComponent(() =>
      loadModule("./personaldetails_list.vue", options)
    ),
  },
  data: function () {
    return {
      id:
        Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15),
      edit: false,
    };
  },
  methods: {
    focus: function (id) {
      window.setTimeout(function () {
        document.getElementById(id).focus();
      }, 100);
    },
    parseHtml: function (html) {
      var temp = document.createElement("span");
      temp.innerHTML = html;
      temp.childNodes.forEach(function parseHtmlChild(node) {
        if (node.nodeType == 1) {
          if (
            !["P", "BR", "SPAN", "B", "I", "U", "S", "OL", "UL", "LI"].includes(
              node.nodeName
            )
          ) {
            var p = document.createElement("P");
            p.innerHTML = node.innerHTML;
            node.parentNode.replaceChild(p, node);
          }
        }
        if (node.style) {
          Array.from(node.style).forEach(function (key, value) {
            if (!["color", "background-color", "font-weight"].includes(key)) {
              node.style[key] = null;
            }
          });
        }
        if (node.attributes) {
          Array.from(node.attributes).forEach(function (key, value) {
            if (!["style"].includes(key)) {
              node[key] = null;
            }
          });
        }
        node.childNodes.forEach(parseHtmlChild);
      });
      return temp.innerHTML;
    },
    addItem: function () {
      this.detail.items.push({
        level: this.detail.level + 1,
        tag: "",
        value: "",
        items: [],
      });
      var main = this.$parent;
      while (main.$options.name != "personaldetails") {
        main = main.$parent;
      }
      main.modalEditPersonalDetail =
        this.detail.items[this.detail.items.length - 1];
      var modal = M.Modal.init(
        document.querySelector("#modalEditPersonalDetail"),
        {
          onCloseEnd: function () {
            M.FloatingActionButton.init(
              document.querySelectorAll(".fixed-action-btn")
            );
          },
        }
      );
      modal.open();
    },
    editItem: function () {
      var main = this.$parent;
      while (main.$options.name != "personaldetails") {
        main = main.$parent;
      }
      main.modalEditPersonalDetail = this.detail;
      var modal = M.Modal.init(
        document.querySelector("#modalEditPersonalDetail"),
        {
          onCloseEnd: function () {
            M.FloatingActionButton.init(
              document.querySelectorAll(".fixed-action-btn")
            );
          },
        }
      );
      modal.open();
    },
    deleteItem: function () {
      if (window.confirm("Confirm you want to remove this item.")) {
        this.$parent.items.splice(this.index, 1);
      }
    },
    upItem: function () {
      var item = this.$parent.items.splice(this.index, 1);
      this.$parent.items.splice(this.index - 1, 0, item[0]);
    },
    downItem: function () {
      var item = this.$parent.items.splice(this.index, 1);
      this.$parent.items.splice(this.index + 1, 0, item[0]);
    },
    tag: function (tag) {
      // From tags.js
      return (
        tags[tag] || {
          name: tag,
          description: "",
          visible: false,
        }
      );
    },
  },
};
</script>
