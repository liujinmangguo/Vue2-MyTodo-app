<template>
  <div id="app" class="container">
    <h1 v-text="title" class="title"></h1>

    <div class="task-add" >
      <input type="text" v-model="newItem" v-on:keyup.enter="addNewItem" placeholder="eg.learn " autofocus autocomplete="off">
      <button type="submit" @click.stop="addNewItem">add</button>
    </div>

    <div class="task-list"> 
      <div class="task-item" v-for="(item,index) in items" v-bind:class="{finish: item.isFinish}" @dblclick="showItem(index)">
        <span >
          <span class="fl"><input type="checkbox" v-bind:checked="item.isFinish" v-on:click="isFinish(item)"></span>
          <span class="fl">{{index+1}}</span>
          <span class="task-content">{{item.label}}</span>
        </span>
        <span class="action">
          <span class="delete" @click="deleteItem(index)">删除</span>
          <span class="detail" @click="showItem(index)">详情</span>
        </span>  
      </div>
    </div>

    <div :class="{mask: isShow}" @click.stop.prevent="hideItem()"></div>

    <div class="task-detail" v-if="isShow" >
      <div class="content">{{detail.content}}</div>
      <textarea class="desc" v-model="detail.desc"></textarea>
      <div class="remind">
        <input type="date" v-model="detail.date">
        <button @click.prevent="saveDetail(detail.desc, detail.date, detail.index)">save</button>
      </div>
    </div> 
    
  </div>
</template>

<script>
import Store from './store.js';
// Store 位 {fetch(){...},save(){...}}

export default {
  name: 'app',
  data(){
    return {
      title: 'My Todo List',
      items: Store.fetch(),
      newItem: '',
      detail: {
        content: '',
        desc: '',
        date: '',
      },
      isShow: false,
    }
  },

  watch: {
    items: {
      handler: function(item){
        Store.save(item)
        // console.log(item);
      },
      deep: true
    }
  },

  methods: {
    toggleFininsh: function (item) {
      item.isFinish = !item.isFinish;
    },

    addNewItem: function(){
      (this.newItem) ? this.items.push({
        label: this.newItem,
        detail: {
          index: '',
          content: '',
          desc: '',
          date: ''
        },
        isFinish: false,
        isShowDetail: false
      }) : null;
      this.newItem = '';
    },

    deleteItem: function(index){
      this.items.splice(index, 1);
    },

    showItem: function(index){
      console.log(index);
      this.isShow = true;
      this.detail.content = this.items[index].label;
      this.detail.desc = this.items[index].detail.desc;
      this.detail.date = this.items[index].detail.date;
      return this.detail.index = index;
    },

    saveDetail: function(desc,date,index){
      // console.log(index,desc,date);

      this.items[index].detail.desc = desc;
      this.items[index].detail.date = date;

      this.isShow = false;
    },

    hideItem: function(){
      this.isShow = false;
      // console.log(this.items[index], index)

    },

    isFinish: function(item){
      item.isFinish = !item.isFinish;
    }

  }
  
}
</script>

<style>
@import './base.css';

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.finish{
  text-decoration: line-through;
  background-color: #ccc;
}
</style>
