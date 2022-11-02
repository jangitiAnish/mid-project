<template>
  <div>
    <h1>hello</h1>
    <p v-for="(contact, index) in contacts" :key="contact.id">
      {{ contact.contactName }}
      <b-button @click="editcontact(contact.id)"
        ><i class="fa fa-edit"></i
      ></b-button>
      <b-button @click="deletecontact(contact.id)"
        ><i class="fa fa-trash"></i
      ></b-button>
    </p>
    <input type="text" value="" v-model="contact" />
    <b-button
      type="submit"
      variant="primary"
      v-if="isEdit"
      @click="updatecontact()"
      >Update</b-button
    >
    <b-button
      type="submit"
      variant="primary"
      v-if="!isEdit"
      @click="savecontact()"
      >Submit</b-button
    >
  </div>
</template>

<script>
import Vue from "vue";
export default {
  data() {
    return {
      contacts: [],
      contact: "",
      isEdit: false
    };
  },
  created() {
    Vue.axios.get("/contacts", {
      headers: { Authorization: `Bearer ${this.idToken}` }
    });
    console
      .log(res => {
        this.contacts = res.data;
      })
      .catch(err => {
        console.log(err);
      });
  },
  methods: {
    savecontact() {
      console.log(this.contact);
      let contact = this.contact;
      if (contact) {
        Vue.axios
          .post(
            "/contacts",
            { contactName: contact },
            { headers: { Authorization: `Bearer ${this.idToken}` } }
          )
          .then(res => {
            if (res.data.error) {
              console.log(res.data.error);
            } else {
              console.log("success");
              console.log(res.data);
              this.contacts.push(res.data);
              console.log(this.contacts);
              this.contact = "";
            }
          })
          .catch(err => {
            console.log(err);
          });
      } else {
        console.log("contact should not be empty");
      }
    },
    deletecontact(contactId) {
      let contactid = contactId;
      console.log(contactid);
      Vue.axios
        .delete(`/contacts/${contactid}`, {
          headers: { Authorization: `Bearer ${this.idToken}` }
        })
        .then(res => {
          if (res.data.error) {
            console.log(res.data.error);
          } else {
            console.log("deleted contact");
            console.log(res);
            console.log(this.contacts);
            res.status === 200
              ? (this.contacts = this.contacts.filter(
                  contact => contact.id !== contactid
                ))
              : console.log("error deleting contact");
          }
        })
        .catch(err => {
          console.log(err);
        });
    },
    editcontact(contactId) {
      console.log(contactId);
      let selectedcontact = this.contacts.filter(
        contact => contact.id === contactId
      )[0].contactName;
      console.log(selectedcontact);
      this.contact = selectedcontact;
      this.isEdit = true;
    }
  }
};
</script>
