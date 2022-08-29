<template>
 <div id="app">
  <h1 class="subtitle has-text-centered">Bucket List:</h1>
  <hr>
  <div class="field has-addons">
<div class="control is expanded">
 
  <input class="input" type="text" v-model="description" placeholder="Go to Anywhere.....">
</div>
<div class="control">
  <a class="button is-danger"  @click="addItem"> ADD </a>
</div>
  </div>
  <div class="notification" v-for="(items,i) in item" :key="items.id">
    <div class="columns">
      <input class="column input" v-if="isSelected(items)" v-model="editedDescription">
    <p v-else class="column">
      <span class="tag is-primary">{{i+1}}</span>
      {{ items.description }}
      </p>
      <div class="column is-narrow">
        <span class="icon has-text-primary" @click="isSelected(items) ? unselect() : select(items)">
            <i class="material-icons">{{ isSelected(items) ? 'close' : 'edit'}}</i>
        </span>
        <span class="icon has-text-info" @click="isSelected(items) ? updateItem(items,i): removeItem(items,i)">
            <i class="material-icons">{{ isSelected(items) ? 'save' : 'delete'}}</i>
        </span>
      </div>
      </div>
  </div>
 </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',
  data(){
    return{
      item:[],
      description:'',
      editedDescription:'',
      selected:{}
    };
  },
  async mounted(){
    const response = await axios.get("api/bucketListItem/")
    this.item=response.data;
  },
  methods: {
    async addItem(){
      // console.log("without description")
      if(this.description.length > 0){
        //  console.log("with description")
        const response = await axios.post("api/bucketListItem/",{description:this.description});
      this.item.push(response.data)
      this.description=''
      }
    },
    async updateItem(items,i){
      const response = await axios.put("api/bucketListItem/"+items._id,{description:this.editedDescription});
      this.item[i]=response.data
      this.unselect();
    },
    async removeItem(items,i) {
        await axios.delete("api/bucketListItem/"+items._id);
        this.item.splice(i,1)
    },
    select(items){
      this.selected=items;
      this.editedDescription=items.description
    },
  unselect(){
    this.selected={};
      this.editedDescription=''
  },
  isSelected(items){
    return items._id===this.selected._id
  }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.icon {
  cursor: pointer;
}
</style>
