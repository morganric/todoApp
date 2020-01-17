<template>
  <div>
    <b-container>
      <b-row>
        <b-col>
          <h2>{{content}}</h2>
          <hr/>
        </b-col>
      </b-row>
      <b-row>
        <b-col>
          <b-button variant="success" block v-b-toggle.collapse-1 >Add An Item</b-button>
            <b-collapse id="collapse-1" class="mt-2">
            <b-card class="text-left">

              <b-form @submit="onSubmit" @reset="onReset" v-if="show">
                <b-form-group
                  id="input-group-title"
                  label="ToDo Title"
                  label-for="input-1"
                  description="Give your item a title."
                >
                  <b-form-input
                    id="input-title"
                    v-model="form.title"
                    type="text"
                    required
                    placeholder=""
                  ></b-form-input>
                </b-form-group>

                <b-form-group
                  id="input-group-description"
                  label="ToDo description"
                  label-for="input-description"
                  description="Describe your item."
                >
                  <b-form-textarea
                    id="input-2"
                    v-model="form.description"
                  ></b-form-textarea>
                </b-form-group>

                <b-form-select v-model="form.category" :options="categories" size="sm" required ></b-form-select>
                <br/>
                <br/>
                <b-button type="submit" variant="info" >Submit</b-button>
                <b-button type="reset" variant="secondary">Reset</b-button>
              </b-form>
            </b-card>
          </b-collapse>
          <br/>
          <b-button variant="info" block @click="sortList" >Sort List</b-button>
          <br/>
          <div role="tablist" >
            <b-card no-body class="mb-1 text-left" v-for="(item, index) in items" >
              <b-card-header header-tag="header" class="p-1" role="tab">
                <b-button  href="#" v-b-toggle="'accordion-' + index" variant="default" class="text-left">{{ item.title }} <small>({{ categories.filter(obj => {return obj.value === item.category})[0]["text"] }})</small>
                </b-button>
                <b-button class="float-right" size="sm" variant="danger" @click="deleteItem(index)">X</b-button>
                  <span class="float-right">&nbsp;</span>
                  <b-button class="float-right" size="sm" variant="info" v-b-modal="'modal-' + index" @click="editItem(index)">Edit</b-button>
              </b-card-header>
              <b-collapse v-bind:id="'accordion-' + index" accordion="my-accordion" role="tabpanel">
                <b-card-body>
                  <b-card-text>{{ item.description }}</b-card-text>
                </b-card-body>
              </b-collapse>
            </b-card>

            <div v-for="(item, index) in items" >
              <b-modal v-bind:id="'modal-' + index" title="Editing" hide-footer>
                    <!-- edit item form -->
                    <b-form @submit="onUpdate" v-if="editingId === index">
                      <b-form-group
                        id="input-group-title"
                        label="Title"
                        label-for="input-1"
                        description="Give your item a title."
                      >
                        <b-form-input
                          id="input-title"
                          v-model="editing.title"
                          type="text"
                          required
                          placeholder=""
                        ></b-form-input>
                      </b-form-group>

                      <b-form-group
                        id="input-group-description"
                        label="Description"
                        label-for="input-description"
                        description="Describe your item."
                      >
                        <b-form-textarea
                          id="input-2"
                          v-model="editing.description"
                        ></b-form-textarea>
                      </b-form-group>

                      <b-form-group
                        id="input-group-category"
                        label="Category"
                        label-for="input-category"
                      >
                        <b-form-select v-model="editing.category" :options="categories" size="sm" required ></b-form-select>
                      </b-form-group>
                      <br/>
                      <br/>
                      <b-button type="submit" variant="info"  @click="$bvModal.hide('modal-' + index)">Update</b-button>
                      <b-button type="reset" variant="secondary">Reset</b-button>
                      <b-button variant="secondary" class="float-right" @click="$bvModal.hide('modal-' + index)">Cancel</b-button>
                    </b-form>
              </b-modal>
            </div>
            
          </div>
          <br/>
          <b-button variant="secondary" block @click="deleteAllItems" v-if="items.length > 0" >Clear List</b-button>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import firebase from 'firebase'
export default {
  name: 'Main',
  data () {
    return {
      content: 'My List',
      categories: [
          { value: null, text: 'Please select an option' },
          { value: "a", text: 'Life Changing' },
          { value: "b", text: 'Important' },
          { value: "c", text: 'Meh' }
        ],
      form: {
        title: "",
        description: "",
        category: ""
      },
      items: [],
      show: true,
      editingId: null,
      editing: null
    }
  },
  methods: {

    deleteAllItems: function() {
      var that = this;
      console.log("items: ", that.items);
      this.items = [];
      window.localStorage.setItem('items', []);
      console.log("items: ", that.items);

    },

    deleteItem: function(id) {
      this.items.shift(id);
      window.localStorage.setItem('items', JSON.stringify(this.items));
    },

    editItem: function(id) {
      this.editingId = id;
      this.editing = this.items[id];
    },

    onSubmit: function(evt) {
        evt.preventDefault();
        var that = this;

        that.items.push(that.form);
        window.localStorage.setItem('items', JSON.stringify(that.items));
        that.items = items;

        // collapes form?
    },

    onUpdate: function(evt) {
        evt.preventDefault();
        var that = this;

        that.items[this.editingId] = that.editing;
        window.localStorage.setItem('items', JSON.stringify(that.items));

    },
    
    onReset: function(evt) {
        evt.preventDefault()
        // Reset our form values
        this.form.title = ''
        this.form.description = ''
        this.form.category = null
        this.form.checked = []
        // Trick to reset/clear native browser form validation state
        this.show = false
        this.$nextTick(() => {
          this.show = true
        })

    },

    sortList: function() {
      this.items = this.items.sort((a, b) => a.category.localeCompare(b.category));
    }

  },
  computed: {
  },

  created: function() {
    var that = this;

    // initialise

    if (window.localStorage.getItem('items')) {

      that.items = JSON.parse(window.localStorage.getItem('items'));
      console.log("items: ", that.items);

    } else {

      var items = [
          { category: "a", title: 'Add Item', description: 'Button and / or form for adding new items' },
          { category: "b", title: 'Remove Item', description: 'Button and / or form for deleting items' },
          { category: "c", title: 'Edit Item', description: 'Button and / or form for editing items' },
          { category: "a", title: 'Order Items', description: 'Order by priority / category' }
        ];

      window.localStorage.setItem('items', JSON.stringify(items));
      that.items = JSON.parse(window.localStorage.getItem('items'));
      console.log("initialised: ", that.items);

    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
