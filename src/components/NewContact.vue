<template>
  <section class="container">
    <h1>Add New Contact</h1>

    <div>
      <form novalidate class="md-layout" @submit.prevent="validateUser">
        <md-card class="md-layout-item md-size-50 md-small-size-100">
          <!-- <md-card-header>
            <div class="md-title">{{}}</div>
          </md-card-header> -->

          <md-card-content>
            <div class="md-layout md-gutter">
              <div class="md-layout-item md-small-size-100">
                <md-field :class="getValidationClass('firstname')">
                  <label for="first-name">First Name</label>
                  <md-input
                    name="firstname"
                    id="firstname"
                    autocomplete="given-name"
                    v-model="form.firstname"
                    :disabled="sending"
                  />
                  <span class="md-error" v-if="!$v.form.firstname.required"
                    >The first name is required</span
                  >
                  <span
                    class="md-error"
                    v-else-if="!$v.form.firstname.minlength"
                    >Invalid first name</span
                  >
                </md-field>
              </div>

              <div class="md-layout-item md-small-size-100">
                <md-field :class="getValidationClass('lastname')">
                  <label for="lastname">Last Name</label>
                  <md-input
                    name="lastname"
                    id="lastname"
                    autocomplete="family-name"
                    v-model="form.lastname"
                    :disabled="sending"
                  />
                  <span class="md-error" v-if="!$v.form.lastname.required"
                    >The last name is required</span
                  >
                  <span class="md-error" v-else-if="!$v.form.lastname.minlength"
                    >Invalid last name</span
                  >
                </md-field>
              </div>
            </div>

            <md-field :class="getValidationClass('emailaddress')">
              <label for="email">Email</label>
              <md-input
                type="email"
                name="emailaddress"
                id="emailaddress"
                autocomplete="emailaddress"
                v-model="form.emailaddress"
                :disabled="sending"
              />
              <span class="md-error" v-if="!$v.form.emailaddress.required"
                >The email is required</span
              >
              <span class="md-error" v-else-if="!$v.form.emailaddress.email"
                >Invalid email</span
              >
            </md-field>
            <md-field :class="getValidationClass('phonenumber')">
              <label for="phonenumber">Phone Number</label>
              <md-input
                type="text"
                name="phonenumber"
                id="phonenumber"
                autocomplete="phonenumber"
                v-model="form.phonenumber"
                :disabled="sending"
              />
              <span class="md-error" v-if="!$v.form.phonenumber.required"
                >The Phone Number is required</span
              >
              <span class="md-error" v-else-if="!$v.form.phonenumber"
                >Invalid number</span
              >
            </md-field>
          </md-card-content>

          <md-progress-bar md-mode="indeterminate" v-if="sending" />

          <md-card-actions>
            <md-button type="submit" class="md-primary" :disabled="sending">{{
              $route.params.person ? "Update Contact" : "Create Contact"
            }}</md-button>
          </md-card-actions>
        </md-card>

        <md-snackbar :md-active.sync="userSaved"
          >The user {{ lastUser }} was saved with success!</md-snackbar
        >
      </form>
    </div>
  </section>
</template>

<script>
import db from './firebaseInit'
import { validationMixin } from 'vuelidate'
// const pattern = /^((\+92)|(0092))-{0,1}\d{3}-{0,1}\d{7}$|^\d{11}$|^\d{4}-\d{7}$/;
import { required, email, minLength } from 'vuelidate/lib/validators'
let documentRef = {}
export default {
  name: 'new-contact',
  mixins: [validationMixin],

  data: () => ({
    form: {
      firstname: null,
      lastname: null,
      phonenumber: null,
      emailaddress: null
    },
    userSaved: false,
    sending: false,
    lastUser: null
  }),
  validations: {
    form: {
      firstname: {
        required,
        minLength: minLength(3)
      },
      lastname: {
        required,
        minLength: minLength(3)
      },
      emailaddress: {
        required,
        email
      },
      phonenumber: {
        required
      }
    }
  },
  computed: {},
  mounted () {
    if (this.$route.params.person) {
      db.collection('contacts')
        .where('slug', '==', this.$route.params.person)
        .get()
        .then((querySnapshot) => {
          if (querySnapshot) {
            documentRef['docId'] = querySnapshot.docs[0].id
            querySnapshot.forEach((doc) => {
              const resp = doc.data()
              documentRef['slug'] = resp.slug
              this.form.firstname = resp.firstname
              this.form.lastname = resp.lastname
              this.form.emailaddress = resp.emailaddress
              this.form.phonenumber = resp.phonenumber
            })
          }
        })
    }
  },

  methods: {
    getValidationClass (fieldName) {
      const field = this.$v.form[fieldName]

      if (field) {
        return {
          'md-invalid': field.$invalid && field.$dirty
        }
      }
    },
    saveContact () {
      const slug = this.generateUUID()
      db.collection('contacts')
        .add({
          firstname: this.form.firstname,
          lastname: this.form.lastname,
          emailaddress: this.form.emailaddress,
          phonenumber: this.form.phonenumber,
          slug: slug
        })
        .then((docRef) => {
          console.log('Document written with ID: ', docRef.id)
          this.$router.push(`/${slug}/success`)
        })
        .catch(function (error) {
          console.error('Error adding document: ', error)
        })
    },
    updateContact () {
      console.log('updateContact -> docId', documentRef)
      console.log('form', this.form)
      db.collection('contacts')
        .doc(documentRef.docId)
        .set({
          firstname: this.form.firstname,
          lastname: this.form.lastname,
          emailaddress: this.form.emailaddress,
          phonenumber: this.form.phonenumber,
          slug: documentRef.slug
        })
        .then(() => {
          this.$router.push(`/`)
        })
        .catch(function (error) {
          console.error('Error adding document: ', error)
        })
    },

    generateUUID () {
      let d = new Date().getTime()
      let uuid = 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(
        /[xy]/g,
        function (c) {
          let r = (d + Math.random() * 16) % 16 | 0
          d = Math.floor(d / 16)
          return (c === 'x' ? r : (r & 0x3) | 0x8).toString(16)
        }
      )
      return uuid
    },
    validateUser () {
      this.$v.$touch()

      if (!this.$v.$invalid) {
        if (!this.$route.params.person) {
          this.saveContact()
        } else {
          this.updateContact()
        }
      }
    }
  }
}
</script>

<style scoped>
h1 {
  text-align: center;
}
.md-layout {
  justify-content: center;
}
section {
  height: 100vh;
}

h1 {
  font-size: 30px;
  margin: 30px 0;
}

.input {
  height: 40px;
}
</style>
