<template>
  <div>
    <Jumper v-if="loading"></Jumper>
    <section class="container">
      <div v-if="!loading">
        <h1>All Contacts ({{ contacts.length }})</h1>
        <div>
          <md-card
            md-with-hover
            v-for="person in contacts"
            v-bind:key="person.slug"
          >
            <md-ripple>
              <md-card-header>
                <md-avatar class="md-avatar-icon md-large right">
                  <md-ripple>{{ person.firstname | firstChar }}</md-ripple>
                </md-avatar>
                <div class="md-title">
                  {{ person.firstname }} {{ person.lastname }}
                </div>
                <div class="md-subhead">{{ person.phonenumber }}</div>
              </md-card-header>

              <md-card-content>
                Lorem ipsum dolor sit amet, consectetur adipisicing elit. Optio
                itaque ea, nostrum odio. Dolores, sed accusantium quasi non.
              </md-card-content>

              <md-card-actions>
                <router-link
                  :to="{
                    name: 'view-contact',
                    params: { person: person.slug },
                  }"
                  ><md-button class="md-raised md-primary"
                    >View Person
                  </md-button></router-link
                >
              </md-card-actions>
              <md-card-actions>
                <router-link
                  :to="{
                    name: 'edit-contact',
                    params: { person: person.slug },
                  }"
                  ><md-button class="md-raised md-primary"
                    >Edit Person
                  </md-button></router-link
                >
              </md-card-actions>
              <md-card-actions>
                <md-button
                  class="md-raised md-primary"
                  v-on:click="deleteContact(person.id)"
                >
                  Delete Person
                </md-button>
              </md-card-actions>
            </md-ripple>
          </md-card>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import db from './firebaseInit'
import { Jumper } from 'vue-loading-spinner'
export default {
  name: 'home',
  components: {
    Jumper
  },
  data () {
    return {
      contacts: [],
      loading: true
    }
  },
  created () {
    this.getContact()
  },
  methods: {
    async getContact () {
      db.collection('contacts')
        .get()
        .then((querySnapshot) => {
          this.loading = false
          if (querySnapshot) {
            querySnapshot.forEach((doc) => {
              let data = {
                id: doc.id,
                firstname: doc.data().firstname,
                lastname: doc.data().lastname,
                emailaddress: doc.data().emailaddress,
                phonenumber: doc.data().phonenumber,
                slug: doc.data().slug
              }
              this.contacts.push(data)
            })
          }
        })
    },
    deleteContact (id) {
      db.collection('contacts').doc(id).delete()
    }
  },

  filters: {
    firstChar (firstname) {
      return firstname.split('')[0].toUpperCase()
    }
  }
}
</script>

<style lang="scss" scoped>
.spinner {
  top: 50% !important;
  left: 50% !important;
  position: fixed !important;
}
.md-card {
  width: 320px;
  margin: 4px;
  display: inline-block;
  vertical-align: top;
  .md-button-content {
    a {
      color: white !important;
    }
  }
}
h1 {
  font-size: 30px;
  margin: 30px 0;
}
.user-list {
  margin-top: 30px;
  background-color: white;
  padding: 20px;
  box-shadow: 0 0 5px 0 rgba(0, 0, 0, 0.05);
  .column {
    height: 95px;
  }
  .inner {
    .left {
      width: 50%;
      float: left;
      text-align: left;
    }
    .right {
      width: 50%;
      float: left;
      text-align: left;
      p {
        width: 100%;
        text-align: left;
      }
    }
  }

  .right {
    display: flex;
    align-items: center;
    justify-content: center;
    button {
      background: #4b75ff;
    }
  }
  .user-list__header {
    font-size: 20px;
    font-weight: 700;
  }
  .user-list__sub {
    font-size: 15px;
    margin-top: 10px;
  }
}
@keyframes placeHolderShimmer {
  0% {
    background-position: -468px 0;
  }
  100% {
    background-position: 468px 0;
  }
}
.animated-background__header {
  -webkit-animation-duration: 1s;
  animation-duration: 1s;
  -webkit-animation-fill-mode: forwards;
  animation-fill-mode: forwards;
  -webkit-animation-iteration-count: infinite;
  animation-iteration-count: infinite;
  -webkit-animation-name: placeHolderShimmer;
  animation-name: placeHolderShimmer;
  -webkit-animation-timing-function: linear;
  animation-timing-function: linear;
  background: #f6f7f8;
  background: #eeeeee;
  background: -webkit-gradient(
    linear,
    left top,
    right top,
    color-stop(8%, #eeeeee),
    color-stop(18%, #dddddd),
    color-stop(33%, #eeeeee)
  );
  background: -webkit-linear-gradient(
    left,
    #eeeeee 8%,
    #dddddd 18%,
    #eeeeee 33%
  );
  background: linear-gradient(to right, #eeeeee 8%, #dddddd 18%, #eeeeee 33%);
  -webkit-background-size: 800px 104px;
  background-size: 800px 104px;
  height: 20px;
  width: 400px;
  position: relative;
}
.animated-background__sub {
  -webkit-animation-duration: 1s;
  animation-duration: 1s;
  -webkit-animation-fill-mode: forwards;
  animation-fill-mode: forwards;
  -webkit-animation-iteration-count: infinite;
  animation-iteration-count: infinite;
  -webkit-animation-name: placeHolderShimmer;
  animation-name: placeHolderShimmer;
  -webkit-animation-timing-function: linear;
  animation-timing-function: linear;
  background: #f6f7f8;
  background: #eeeeee;
  background: -webkit-gradient(
    linear,
    left top,
    right top,
    color-stop(8%, #eeeeee),
    color-stop(18%, #dddddd),
    color-stop(33%, #eeeeee)
  );
  background: -webkit-linear-gradient(
    left,
    #eeeeee 8%,
    #dddddd 18%,
    #eeeeee 33%
  );
  background: linear-gradient(to right, #eeeeee 8%, #dddddd 18%, #eeeeee 33%);
  -webkit-background-size: 800px 104px;
  background-size: 800px 104px;
  height: 20px;
  width: 200px;
  position: relative;
}
</style>
