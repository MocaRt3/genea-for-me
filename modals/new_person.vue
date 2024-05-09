<template>
  <div id="modalNewPerson" class="modal">
    <div class="modal-content">
      <template v-if="true">
        <h4>Add</h4>
        <p>
          <label>
            <input
              type="radio"
              value="parent"
              v-model="newPerson.type"
              :disabled="parents().length >= 2" />
            <span>
              Parent
              <span v-if="parents().length >= 2">
                - Already has two or more parents.
              </span>
            </span>
          </label>
        </p>
        <p>
          <label>
            <input type="radio" value="relation" v-model="newPerson.type" />
            <span>Relation</span>
          </label>
        </p>
        <p>
          <label>
            <input
              type="radio"
              value="sibling"
              v-model="newPerson.type"
              :disabled="parents().length == 0" />
            <span>
              Sibling
              <span v-if="parents().length == 0">- Add a Parent first.</span>
            </span>
          </label>
        </p>
        <p>
          <label>
            <input
              type="radio"
              value="child"
              v-model="newPerson.type"
              :disabled="relations().length == 0" />
            <span>
              Child
              <span v-if="relations().length == 0">
                - Add a Relation first.
              </span>
            </span>
          </label>
        </p>
        <p>
          <label>
            <input type="radio" value="unrelated" v-model="newPerson.type" />
            <span>Unrelated</span>
          </label>
        </p>
      </template>
      <template v-if="newPerson.type == 'child'">
        <h4>Relation</h4>
        <p v-for="relation in relations()">
          <label>
            <input
              type="radio"
              :value="relation"
              v-model="newPerson.relation" />
            <span>{{ relation.partner.name }}</span>
          </label>
        </p>
      </template>
      <template
        v-if="
          (newPerson.type != 'child' && newPerson.type != 'unrelated') ||
          (newPerson.type == 'child' && newPerson.relation)
        ">
        <h4>How</h4>
        <p>
          <label>
            <input type="radio" value="new" v-model="newPerson.action" />
            <span>Create new person</span>
          </label>
        </p>
        <p>
          <label>
            <input type="radio" value="link" v-model="newPerson.action" />
            <span>Link with existing person</span>
          </label>
        </p>
      </template>
      <template
        v-if="newPerson.action == 'new' || newPerson.type == 'unrelated'">
        <h4>New Person</h4>
        <div class="row">
          <div class="col s6">
            <div class="input-field">
              <label for="givenName" class="active">Given Name</label>
              <input id="givenName" v-model="newPerson.givenName" type="text" />
            </div>
          </div>
          <div class="col s6">
            <div class="input-field">
              <label for="surName" class="active">Surname</label>
              <input id="surName" v-model="newPerson.surName" type="text" />
            </div>
          </div>
          <div class="col s6">
            <div class="input-field">
              <label for="sex" class="active">Sex</label>
              <input
                id="sex"
                v-model="newPerson.sex"
                class="autocomplete"
                type="text" />
            </div>
          </div>
          <div class="col s12">
            <a
              class="btn waves-effect waves-light modal-close"
              v-on:click="create()">
              <i class="material-icons left">person_add</i>
              Create
            </a>
          </div>
        </div>
      </template>
      <template v-if="newPerson.action == 'link'">
        <h4>Existing Person</h4>
        <div class="row">
          <div class="col s6">
            <div class="input-field">
              <i class="material-icons prefix">person</i>
              <input
                id="existingPerson"
                type="text"
                class="autocomplete"
                placeholder="Find Person" />
            </div>
          </div>
          <div class="col s12">
            <a
              class="btn waves-effect waves-light modal-close"
              v-on:click="link()">
              <i class="material-icons left">link</i>
              Link
            </a>
          </div>
        </div>
      </template>
    </div>
    <div class="modal-footer">
      <a class="modal-close waves-effect waves-green btn-flat">Close</a>
    </div>
  </div>
</template>

<script>
export default {
  props: ["person"],
  data: function () {
    return {
      newPerson: {
        givenName: " ",
        surName: " ",
        sex: " ",
      },
    };
  },
  watch: {
    newPerson: {
      handler: function (val, oldVal) {
        if (val.action == "link") {
          var data = this.newPerson;
          window.setTimeout(function () {
            M.Autocomplete.init(document.querySelector("#existingPerson"), {
              data: (function () {
                var persons = this.$root.stamboom.getPersons();
                persons = Object.fromEntries(
                  persons.map(person => [person.caption])
                );
                return persons;
              })(),
              onAutocomplete: function (selection) {
                var person = stamboom
                  .getPersons()
                  .find(person => person.caption == selection);
                data.person = person;
              },
            });
          }, 200);
        }
      },
      deep: true,
    },
  },
  methods: {
    open: function () {
      var that = this;
      var modal = M.Modal.init(document.querySelector("#modalNewPerson"), {
        onCloseEnd: function () {
          that.newPerson = {};
        },
      });
      modal.open();
    },
    create: function () {
      var newPerson = this.$root.stamboom.addPerson(
        this.newPerson.givenName,
        this.newPerson.surName,
        this.newPerson.sex
      );
      if (this.newPerson.type == "parent") {
        var relationId = null;
        if (this.$root.stamboom.getParents(this.person.id).length == 0) {
          // Mock relation in order to get a FAM record
          relationId = this.$root.stamboom.addRelation(newPerson.id);
        } else if (this.$root.stamboom.getParents(this.person.id).length == 1) {
          // Remove existing one-parent FAM record and recreate for both parents
          var parent = this.$root.stamboom.getParents(this.person.id)[0].id;
          var tempRelationId = this.$root.stamboom.getRelations(parent)[0].id;
          this.$root.stamboom.removeRelation(tempRelationId);
          relationId = this.$root.stamboom.addRelation(parent, newPerson.id);
        } else {
          return;
        }
        this.$root.stamboom.addChild(relationId, this.person.id);
        // Add child
      } else if (this.newPerson.type == "relation") {
        this.$root.stamboom.addRelation(this.person.id, newPerson.id);
        this.$root.stamboom.select(this.person.id);
      } else if (this.newPerson.type == "sibling") {
        this.$root.stamboom.addSibling(this.person.id, newPerson.id);
        this.$root.stamboom.select(this.person.id);
      } else if (this.newPerson.type == "child") {
        this.$root.stamboom.addChild(this.newPerson.relation.id, newPerson.id);
      } else if (this.newPerson.type == "unrelated") {
        // Switch to the new person, no further linkage needed.
        this.person.id = newPerson.id;
      }
      // Force update
      this.$root.stamboom.select(this.person.id);
    },
    link: function () {
      if (this.newPerson.type == "parent") {
        var relationId = null;
        if (this.$root.stamboom.getParents(this.person.id).length == 0) {
          // Mock relation in order to get a FAM record
          relationId = this.$root.stamboom.addRelation(
            this.newPerson.person.id
          );
        } else if (this.$root.stamboom.getParents(this.person.id).length == 1) {
          // Remove existing one-parent FAM record and recreate for both parents
          var parent = this.$root.stamboom.getParents(this.person.id)[0].id;
          var tempRelationId = this.$root.stamboom.getRelations(parent)[0].id;
          this.$root.stamboom.removeRelation(tempRelationId);
          relationId = this.$root.stamboom.addRelation(
            parent,
            this.newPerson.person.id
          );
        } else {
          return;
        }
        this.$root.stamboom.addChild(relationId, this.person.id);
        // Add child
      } else if (this.newPerson.type == "relation") {
        this.$root.stamboom.addRelation(
          this.person.id,
          this.newPerson.person.id
        );
        this.$root.stamboom.select(this.person.id);
      } else if (this.newPerson.type == "sibling") {
        this.$root.stamboom.addSibling(
          this.person.id,
          this.newPerson.person.id
        );
        this.$root.stamboom.select(this.person.id);
      } else if (this.newPerson.type == "child") {
        this.$root.stamboom.addChild(
          this.newPerson.relation.id,
          this.newPerson.person.id
        );
      } else if (this.newPerson.type == "unrelated") {
        // Switch to the new person, no further linkage needed.
        this.person.id = this.newPerson.person.id;
      }
      // Force update
      this.$root.stamboom.select(this.person.id);
    },

    relations: function () {
      return this.$root.stamboom.getRelations(this.person.id);
    },
    parents: function () {
      return this.$root.stamboom.getParents(this.person.id);
    },
  },
};
</script>
