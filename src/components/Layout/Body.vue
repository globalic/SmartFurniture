<template>
  <div>
    <transition :name="routeChange">
      <!-- Body Container -->
      <div class="container-fluid body">
        <transition name="body-fade">
          <div>
            <div v-if="!auth">
              <div>
                <button class="btn btn-primary btn-lg sign-in" @click="login()"><i class="fa fa-facebook-square" aria-hidden="true"></i> Login with Facebook</button>
              </div>
            </div>
          </div>
        </transition>
        <transition :name="transitionName" mode="out-in">
          <router-view :Auth="auth" :Products="Products" class="body-container"></router-view>
        </transition>
      </div>
    </transition>

  </div>
</template>

<script>
import firebase from 'firebase'
export default {
  name: 'Body',
    props: ['Products'],
    created() {
        // default this
        const this_ = this
        // Passdata to other component when login
        firebase.auth().onAuthStateChanged(user => {
            if (user) {
                // return user
                this.auth = user
                // Enable search input
                $('.search > input').prop('disabled', false);
                $('.search > input').removeClass('search-disable');

            } else {
                this.auth = false
                // disable search input
                $('.search > input').prop('disabled', true);
                $('.search > input').addClass('search-disable');
            }
        })
    },
  data() {
    return {
      transitionName: 'slide-left',
      auth: null,
      products: null,
      routeChange: 'slide-left'
    }
  },
  beforeRouteUpdate (to, from, next) {
    const toDepth = to.path.split('/').length
    const fromDepth = from.path.split('/').length
    this.routeChange = toDepth < fromDepth ? 'slide-right' : 'slide-left'
    next()
  },
  methods: {
    login() {
      if (!this.auth) {
        firebase.auth().setPersistence(firebase.auth.Auth.Persistence.SESSION).then( function() {
          const provider = new firebase.auth.FacebookAuthProvider()
            return firebase.auth().signInWithPopup(provider)
        }).catch(function(error) { console.log(error) })
      }
    },
  },
}
</script>




<style>
.sign-in {
  min-width: 20%;
  height: 10%;
  position: absolute;
  top: 45%;
  left: 50%;
  transform: translate(-50%, -50%)
}
body {
  padding: 15px 10px 5px 10px;
  background-image: url('../../assets/loading-bg.jpg');
}
@media (max-width: 768px) {
  body {
    padding-bottom: 65px;
  }
}

/* Container Transition */
.slide-left-enter-active, .slide-left-leave-active {
  transition: opacity .5s;
}
.slide-left-enter, .slide-left-leave-to {
  opacity: 0;
}

/* Body-Fade Transition */
.body-fade-enter-active, .body-fade-leave-active {
    transition: opacity .5s ease;;
}
.body-fade-enter, .body-fade-leave-to {
    opacity: 0;
}


/* Body Transition Auth */
.body {
  transition: all 0.5s ease;
}


/* Body Fade */
.body-fade {
  transition: 0.3s;
}

/* Modal dialog mobile */
@media (max-width: 768px) {
    .modal-dialog {
        padding: 15%;
    }
}
</style>