<template>
  <div class="add">
    <div class="add-item container">
      <h2 class="center-align"> Add New Item</h2>
      <form @submit.prevent="Additem">
        <div class="row">
          <div class="input-field col s8">
            <p class="sub_title">Item Title</p>
            <input type="text" name="title" class="validate" v-model="title">          
          </div>
          <div class="input-field col s8">
            <p class="sub_title">Price</p>       
            <input type="text" name="price" v-model="price">
          </div>
          <div class="input-field col s12">
            <p class="sub_title">Description</p>  
            <textarea id="textarea2" class="materialize-textarea" data-length="120"  v-model="content"></textarea>
          </div>
        </div>
        <div class="row">
          <p class="sub_title">Tag </p>
          <div class="col s4">
            <input type="text" name="tag" v-model="tags[0]">
           <label for="tag"> Tag 1</label>
          </div>
          <div class="col s4">
            <input type="text" name="tag" v-model="tags[1]">
           <label for="tag"> Tag 2</label>
          </div>
          <div class="col s4">
            <input type="text" name="tag" v-model="tags[2]">
           <label for="tag"> Tag 3</label>
          </div>
        </div>

        <div class="divider"></div>
        <!--color pick-->
        <div class="color_pick">
          <p class="sub_title">Color </p>
          <p>
            <label>
              <input type="checkbox"  name="color"  value="Red" v-model="colors"/>
              <span>Red</span>
            </label>
          </p>

          <p>
            <label>
              <input type="checkbox" name="color" value="Yellow" v-model="colors"/>
              <span>Yellow</span>
            </label>
          </p>

          <p>
            <label>
              <input type="checkbox" name="color" value="Green" v-model="colors"/>
              <span>Green</span>
            </label>
          </p>

          <p>
            <label>
              <input type="checkbox" name="color" value="White" v-model="colors"/>
              <span>White</span>
            </label>
          </p>

          <p>
            <label>
              <input type="checkbox" name="color" value="Black" v-model="colors"/>
              <span>Black</span>
            </label>
          </p>
        </div>

        <div class="divider"></div>
        <div class="row quantity">
          <p class="sub_title">Size </p>
          <div class="col s4">
            S
            <div class="input-field inline">
              <input type="text" v-model="S">
            </div>
          </div>
          <div class="col s4">
            M
            <div class="input-field inline">
              <input type="text" v-model="M">
            </div>
          </div>
          <div class="col s4">
            L
            <div class="input-field inline">
              <input type="text" v-model="L">
            </div>
          </div>
        </div>

        <div class="field center-align btn_box">
            <p v-if="feedback" class="red-text">{{feedback}}</p>
            <button class="btn waves-effect waves-light deep-orange lighten-1">Add Item</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import db from '@/firebase/init'
import firebase from 'firebase'
import slugify from 'slugify'
export default {
  name: 'Add',
  data () {
    return {
      title: null,
      price: null,
      content: null,
      tags: [],
      feedback: null,
      slug: null,
      colors: [],
      S: null,
      M: null,
      L: null,
      wish: false,
      writer: null,
      user: null
    }
  },
  created(){
    let ref= db.collection('users')
      //get current user
      ref.where('user_id','==',firebase.auth().currentUser.uid).get()
      .then(snapshot=>{
          snapshot.forEach(doc => {
              this.user =doc.data(),
              this.user.id = doc.id
          })
          console.log('get current user id')
      })
  },
  methods: {
    Additem(){ //
          if(this.title && this.price && this.content){
              this.feedback=null
              //create a slug
              this.slug = slugify(this.title, {
                  replacement: '-',
                  remove: /[$*_+~.()'"!\-:@]/g,//globally.
                  lower: true //lowercase.
              })
              console.log(this.slug);//check slug
 
              db.collection('items').add({
                title: this.title,
                price: this.price,
                content: this.content,
                tags: this.tags,
                colors: this.colors,
                S: this.S,
                M: this.M,
                L: this.L,
                date: Date.now(),
                slug: this.slug,
                writer: this.user.id
              }).then(()=>{
                this.$swal({
                    position: 'center',
                    type: 'success',
                    title: 'regist Product complete',
                    showConfirmButton: false,
                    timer: 1000
                 })
                 this.$router.push({ name: 'Index'})//
              }).catch(err => {
                  console.log(err);
              })

          }else {
              this.feedback= 'Insert product infomaion please!'
          }
      },
  }
}
</script>


<style scoped>
.add{
  width: 70%;
  margin:0 auto;
}
.add h2{
  color: #ff7657;
  font-size: 1.8em;
  font-weight: bold;
}
.sub_title{
  font-weight: bold;
  color: #ff7657;
}
.divider{
  margin: 40px auto;
}

.btn_box{
  margin-bottom: 40px;
}

</style>

