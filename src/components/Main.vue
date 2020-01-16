<template>
  <div>
    <b-container>
      <b-row>
        <b-col>
          <h2>{{content}}</h2>
          <hr/>
          <br/>
        </b-col>
      </b-row>
      <b-row>
        <b-col>

          <b-button variant="success" block v-b-toggle.collapse-1 >Add An Item</b-button>
            <b-collapse id="collapse-1" class="mt-2">
            <b-card class="text-left">
              <b-form @submit="onSubmit" @reset="onReset" v-if="">
                <b-form-group
                  id="input-group-1"
                  label="ToDo Title"
                  label-for="input-1"
                  description="Give your item a title."
                >
                  <b-form-input
                    id="input-1"
                    v-model="item.title"
                    type="text"
                    required
                    placeholder=""
                  ></b-form-input>
                </b-form-group>

                <b-form-group
                  id="input-group-2"
                  label="ToDo description"
                  label-for="input-2"
                  description="Describe your item."
                >
                  <b-form-textarea
                    id="input-2"
                    v-model="item.description"
                  ></b-form-textarea>
                </b-form-group>

                <b-button type="submit" variant="info">Submit</b-button>
                <b-button type="reset" variant="secondary">Reset</b-button>
              </b-form>
            </b-card>
          </b-collapse>
          <br/>
          <br/>
          <div role="tablist" >
            <b-card no-body class="mb-1 text-left" v-for="(item, index) in items" >
              <b-card-header header-tag="header" class="p-1" role="tab">
                <b-button  href="#" v-b-toggle="'accordion-' + index" variant="default" class="text-left">{{ index + 1 }}. {{ item.title }}
                </b-button>
                <b-button class="float-right" size="sm" variant="danger" @click="deleteItem(index)">Delete</b-button>
                  <span class="float-right">&nbsp;</span>
                  <b-button class="float-right" size="sm" variant="info" @click="editItem(index)">Edit</b-button>
              </b-card-header>
              <b-collapse v-bind:id="'accordion-' + index" visible accordion="my-accordion" role="tabpanel">
                <b-card-body>
                  <b-card-text>{{ item.description }}</b-card-text>
                </b-card-body>
              </b-collapse>
            </b-card>
          </div>

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
      content: 'My Super Fun Times ToDo List',
      item: {
        title: null,
        description: null
      },
      items: [
          { position: 1, title: 'Add Item to ToDo List', description: 'Button and / or form for adding new items' },
          { position: 2, title: 'Remove Item to ToDo List', description: 'Button and / or form for deleting items' },
          { position: 3, title: 'Edit Item to ToDo List', description: 'Button and / or form for editing items' },
          { position: 4, title: 'Order items', description: 'Order by priority / category' }
        ]
    }
  },
  methods: {

    deleteItem: (id) => {
      alert("deleteItem: " + id);
    },

    editItem: (id) => {
      alert("editItem: " + id);
    },

    onSubmit: () => {
      var that = this;
      this.item.title = 
      console.log("added item: ", that.item);
    },

    onReset: (id) => {
    }

  },
  computed: {
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
