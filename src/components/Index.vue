<template>
  <div class="container main">
    <div class="search">
      <div class="searching_box input-field col s6">    
          <input id="icon_prefix" type="text" class="validate" v-model="search_data"  placeholder="Searching..">
           <i class="clear material-icons grey-text" v-if="search_data" @click="resetSearch">close</i>
      </div>
    </div>
    <h2>Item List</h2>
    <p class="item_quantity">Total <span>{{items.length}}</span>products</p>
    <div class="sort_ed">
      <div class="sort">
        <a href="#" @click="sortByDate()">Latest</a>
        <a href="#" @click="sortByPrice()">Price</a>
      </div>
      <div class="edit_delete">
        <a href="#" @click="editbtn=true">Edit</a>
        <a href="#" @click="deletebtn=true">Delete</a>
      </div>
    </div>
    <!-- item box -->
    <div class="index">
      <div class="card" v-for="item in filteredItems" :key="item.id">
          <!-- <div class="card-image">
              <img src="@/assets/1.jpg">
          </div> -->
          <div class="card-content">

              <router-link :to="{ name: 'Detail', params: {item_slug: item.slug}}">
                  <h3>{{item.title}}</h3>
              </router-link>

              <p class="price">{{addComma(item.price)}}</p>

              <div class="divider"></div>

              <p class="writer">{{item.writer}}</p>
              <p class="cont">{{item.content}}</p>
                    
              <ul class="tags">
                  <li v-for= "(tag,index) in item.tags" :key="index"  @click="findword">
                    <span class="tag">#{{tag}}</span>
                  </li>
              </ul>
              <p class="date">{{moment(item.date).format(' Y. M. D')}}</p>
              
              <div class="divider"></div>
                <ul class="left_icon">
                  <li v-show="deletebtn" @click="deleteItem(item.id)">
                    <i class="material-icons  grey-text delete">delete_forever</i>
                  </li>
                  <li v-show="editbtn" >
                    <router-link :to="{ name: 'Edit', params: {item_slug: item.slug}}">
                      <i class="material-icons  grey-text">edit</i>
                    </router-link>
                  </li>
                </ul>
                <ul class="right_icon">
                    <li><router-link :to="{ name: 'Detail', params: {item_slug: item.slug}}"><i class="material-icons grey-text">local_grocery_store</i></router-link></li>
                </ul>
              </div>

          </div>
    </div>
  </div>
</template>

<script>
import db from '@/firebase/init'
import firebase from 'firebase'
import moment from 'moment'
import searchMixin from '@/mixins/searchMixin'


export default {
  name: 'Index',
  data () {
    return {
      moment: moment,
      deletebtn: false,
      editbtn: false,
      items: [],
      search_data: '',
      images:[]
    }
  },
  methods: {
    findword: function(e){
        const fw =e.target.innerHTML;
        const fw_cut=fw.slice(1);
        console.dir(fw_cut);
        console.log(e.target.nodeName);
        this.search_data = fw_cut;
    },
    resetSearch(){
      this.search_data = ''
    },
    /***sorting***/
    sortByPrice(){//
      console.table(this.items);
      this.items.sort((a,b)=> parseInt(a.price) < parseInt(b.price) ? -1 : parseInt(a.price) > parseInt(b.price) ? 1 : 0)
    },
    sortByDate(){
      this.items.sort((a,b)=> a.date < b.date ? 1 : a.date > b.date ? -1 : 0)
    },
    addComma(num) {
      var regexp = /\B(?=(\d{3})+(?!\d))/g
      return num.toString().replace(regexp, ',')
    },
    deleteItem(id){ 
      db.collection('items').doc(id).delete()
      .then(() =>{
        this.items = this.items.filter(item =>{
          return item.id !=id 
        })
         this.$swal({
            position: 'center',
            type: 'success',
            title: 'Delete complete',
            showConfirmButton: false,
            timer: 1000
        })
        this.deletebtn=false
      })
      
    },


  },
  created(){
    //fetch data from the firestore
    db.collection('items').get()
    .then(snapshot =>{//컬렉션 하위 문서목록지칭함.
      snapshot.forEach(doc =>{//문서목록중 doc 하나하나에 실행하기.
        let item = doc.data()
        item.id =  doc.id
        this.items.push(item)

      })
    })

    
      // console.log("test LJH", firebase.auth().currentUser)
      //get current user
      //var tempData = firebase.auth().currentUser.uid
      //var tempData = "k3ondeT9zIOc3PuaR7P6ApKd8tu2"

      // db.collection('users').where('user_id','==','tempData').get()
      //   // db.collection('users').where('user_id','==',firebase.auth().currentUser.uid).get()
      //   .then(snapshot=>{
      //     console.log("snapshot", snapshot),
      //       snapshot.forEach(doc => {
      //         console.log("doc data", doc.data());
      //           // this.user =doc.data(),
      //           // this.user.id = doc.id
      //       })
        
      //   console.log('get current user id')
      //       // console.log(this.user.id)
      //   })
    
            //     console.log("test LJH", firebase.auth().currentUser)
            // //get current user
            // var tempData = firebase.auth().currentUser.uid
            // //var tempData = "k3ondeT9zIOc3PuaR7P6ApKd8tu2"
            // db.collection('users').where('user_id','==','tempData').get()
            // // db.collection('users').where('user_id','==',firebase.auth().currentUser.uid).get()
            // .then(snapshot=>{
            //   console.log("snapshot", snapshot),
            //     snapshot.forEach(doc => {
            //       console.log("doc data", doc.data());
            //         // this.user =doc.data(),
            //         // this.user.id = doc.id
            //     })
            
            // console.log('get current user id')
            //     // console.log(this.user.id)
            // })


  },
  mixins:[searchMixin]

}
</script>

<style scoped>
.main{
  position: relative;
}
.search{
    width: 25%;
    position: absolute;
    top:0;
    right: 0;
}
.clear{
  cursor: pointer;
  position: absolute;
  top: 15px;
  right: 5px;
}
.searching_box{
    margin: 0;
}
.searching_box i{
    float:right;
}
.sort_ed{
  overflow:hidden;
}
.sort{
  float: left;
  text-align: center;
  height: 44px;
  line-height: 44px;
}

.sort a{
  color:#666;
  margin: 0 5px;
}
.edit_delete{
  float:right;

}
.edit_delete a{
  display: inline-block;
  border: 1px solid #eee;
  border-radius: 10px;
  padding:5px 10px;
  margin: 5px 5px;
  color:#666;
  font-weight: bold;
}

a:hover{
 color: #ff7657;
}

h2{
  color: #ff7657;
  font-size: 1.8em;
  font-weight: bold;
  text-align: center;
}
.item_quantity{
  text-align: center;
}
.item_quantity span{
  font-weight: bold;
}
.index{
  border-top: 2px solid #ccc;
  padding-top:20px;
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  grid-gap: 10px;
}

.divider {
    margin: 10px 0;
}
.index h3{
  font-size: 1.2em;
  margin:0;
  font-weight: bold;
  color: #ff7657;
}
.index .price{
    font-size: 1.6em;
    margin-top: 10px;  
}

.cont{
    height: 90px;
    padding: 10px 0 0 0;
}
.index .date{
    color: grey;
    text-align: right;
}
.index .colors{
  margin: 30px auto;
}

.index .colors li{
  display: inline-block;
}

.index ul{
  margin: 0;
}
.index .tags {
    margin: 10px 0;
}
.index .tags li{
  display: inline-block;
  margin-right: 10px;
}
.index .tags li:hover{
  color: #ff7657;
  cursor: pointer;
  text-decoration: underline;
}
.index .right_icon{
  float: right;
}
.index .left_icon li{
  display: inline-block;
  cursor: pointer;
  padding :5px 10px 10px 10px;
}
.index .left_icon{
  float: left;
}

.index .right_icon li{
    display: inline-block;
    cursor: pointer;
    padding :5px 10px 10px 10px;
    float: right;
}


.index .delete{
  cursor: pointer;
  color: #aaa;
}
</style>
