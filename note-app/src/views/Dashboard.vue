<!-- eslint-disable vue/no-deprecated-slot-attribute -->
<template>
  <div>
    <mwc-list v-for="(note) in notes" :key="note.id" multi>
      <mwc-list-item twoline hasMeta>
        <span>{{note.title}}</span>
        <span slot="meta" class="material-icons" @click="handleDelete(note.id)">delete</span>
        <span slot="meta" class="material-icons" @click="handleEdit(note.id)">edit</span>
        <span slot="secondary">{{note.description}}</span>
      </mwc-list-item>
      <li divider padded role="separator"></li>
    </mwc-list>
    <mwc-fab class="floatButton" @click="handleAdd" mini icon="add"></mwc-fab>
    <mwc-dialog id="dialog" :heading='heading' scrimClickAction="">
      <div class="formFields">
        <mwc-textfield
          id="text-title"
          outlined
          minlength="3"
          label="Title"
          required>
        </mwc-textfield>
      </div>
      <div class="formFields">
      <mwc-textfield
        id="text-description"
        outlined
        minlength="3"
        label="Description"
        required>
      </mwc-textfield>
      </div>
      <div>
      <mwc-button        
        id="primary-action-button"
        slot="primaryAction"
        @click="actionHandler(isEditing)">
        {{primaryActionText}}
      </mwc-button>
      <mwc-button
        slot="secondaryAction"
        dialogAction="close"
        @click="handleClose">
        Cancel
      </mwc-button>
      </div>
    </mwc-dialog>
  </div>
</template>
<script>
import '@material/mwc-list/mwc-list';
import '@material/mwc-list/mwc-list-item';
import '@material/mwc-fab';
import '@material/mwc-button';
import '@material/mwc-dialog';
import '@material/mwc-textfield';
import { notesData } from '../utils/DummyData';
import { v4 as uuidv4 } from 'uuid';

export default {
  name: 'Dashboard',
  data() {
    return {
      notes: notesData,
      isEditing: -1,     
    }
  },  
  computed:{
    heading() {
      return this.isEditing == -1 ? "Add note" : "Edit note";
    },
    primaryActionText() {
      return this.isEditing == -1 ? "Add" : "Edit";
    }
  },
  methods: {
    actionHandler(id) {
      switch (id) {
        case -1:
          this.handleAddNote();
          break;

        default:
          this.handleEditNote(id);
          break;
      }
    },
    handleDelete(id) {
      const noteToDelete = this.notes.findIndex((item) => (item.id === id));
      this.notes.splice(noteToDelete, 1);
    },
    handleEdit(id) {
      const noteToEdit = this.notes.find((item) => (item.id === id));
      const formDialog = this.$el.querySelector('#dialog');
      let txtTitle = this.$el.querySelector('#text-title');
      let txtDescription = this.$el.querySelector('#text-description');
      if (noteToEdit) {
        txtTitle.value = noteToEdit.title;
        txtDescription.value = noteToEdit.description;
        this.isEditing = noteToEdit.id;
        formDialog.show();
      }
    },
    handleAdd() {
      const formDialog = this.$el.querySelector('#dialog');
      formDialog.show();
    },
    handleAddNote() {
      const formDialog = this.$el.querySelector('#dialog');
      let txtTitle = this.$el.querySelector('#text-title');
      let txtDescription = this.$el.querySelector('#text-description');
      const isValid = txtTitle.checkValidity() && txtDescription.checkValidity();

      if(isValid) {
        const newIndex = uuidv4();
        this.notes.push({id: newIndex, title: txtTitle.value, description: txtDescription.value});

        txtTitle.value ='';
        txtDescription.value = '';
        formDialog.close();
      }
    },
    handleEditNote(id) {
      const formDialog = this.$el.querySelector('#dialog');
      let txtTitle = this.$el.querySelector('#text-title');
      let txtDescription = this.$el.querySelector('#text-description');
      const isValid = txtTitle.checkValidity() && txtDescription.checkValidity();
      const noteToEdit = this.notes.findIndex((item) => (item.id === id));      
      if (isValid && noteToEdit != null) {
        this.notes[noteToEdit].title = txtTitle.value;
        this.notes[noteToEdit].description = txtDescription.value;

        txtTitle.value = '';
        txtDescription.value = '';
        this.isEditing = -1;
        formDialog.close();
      }
    },
    handleClose() {
      let txtTitle = this.$el.querySelector('#text-title');
      let txtDescription = this.$el.querySelector('#text-description');
      const formDialog = this.$el.querySelector('#dialog');

      txtTitle.value ='';
      txtDescription.value = '';
      this.isEditing = -1;      
      formDialog.close();
    }
  },
}
</script>
<style scoped>
  .floatButton {
    position: fixed;
    bottom: 20px;
    right: 20px;
  }

  .formFields {
    margin: 15px;
  }
</style>