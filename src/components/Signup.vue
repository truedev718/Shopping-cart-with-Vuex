<template>
  <div class="signup container">
    <form @submit.prevent="signup" class="card-panel">
        <h2 class="center">Signup</h2>
        <div class="field">
            <label for="email">Email:</label>
            <input type="email" name="email" v-model="email">
        </div>
        <div class="field">
            <label for="password">Password:</label>
            <input type="password" name="password" v-model="password">
        </div>
        <div class="field">
            <label for="alias">Alias:</label>
            <input type="text" name="alias" v-model="alias">
        </div>
        <p class="red-text center" v-if="feedback">{{feedback}}</p>
        <div class="field center">
            <button class="btn waves-effect waves-light deep-orange lighten-1">Signup</button>
        </div>
    </form>
  </div>
</template>

<script>
import slugify from 'slugify'
import db from '@/firebase/init'
import firebase from 'firebase'

export default {
  name: 'Signup',
  data () {
    return {
        email: null,
        password: null,
        alias: null,
        feedback: null,
        slug: null
    }   
  },
  methods: {
      signup(){
        if(this.alias && this.email &&this.password){//
 
            this.slug = slugify(this.alias, {
                replacement : '-',
                remove: /[$*_+~.()'"!\-:@]/g,
                lower: true
            })

            let ref = db.collection('users').doc(this.slug)
            ref.get().then(doc => {
                if(doc.exists){//
                    this.feedback ='Aleady exist Id.'
                } else {

                    firebase.auth().createUserWithEmailAndPassword(this.email, this.password)
                    .then(cred =>{//
                        //console.log(cred.user)
                        ref.set({ //can set some propety to the record
                            alias: this.alias,
                            user_id: cred.user.uid
                        })
                    }).then(() =>{//redirect into main
                        this.$router.push({name: 'Index'})
                    })
                    .catch(err =>{
                        console.log(err)
                        this.feedback = err.message

                    })
                    this.feedback = 'Please wait. registering now...' 
                    this.$swal({
                    position: 'center',
                    type: 'success',
                    title: 'Congratulation! Successfully signed up.',
                    showConfirmButton: false,
                    timer: 2000
                 })
                }
            })
            //console.log(this.slug);
        } else{
            this.feedback = "Please insert your ID."
        }
      }
  }
}
</script>

<style>

.signup{
    max-width: 400px;
    margin-top: 60px;
}

.signup .field{
    margin-bottom: 16px;
}
</style>
