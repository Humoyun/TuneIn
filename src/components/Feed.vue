<template>
  <div>
    <div v-if="loading" class="signal"></div>
    <div :hidden="loading" class="feed animated fadeIn">
      <ul class="feed__list">
        <li class="song" v-for="song in feed" :key="song.id">
          <div class="placeholder placeholder__feed">
            <div @click="cue(song)" class="image image__feed" :style="getBgImg(song.album.cover_small)"></div>
          </div>
          <div class="song__info">
            <span class="title">{{shorten(song.title,25)}}</span>
            <span class="artist">{{song.artist.name}}</span>          
          </div>
          <div class="rank">
            <i @click="likeSong(song)" :class="{'liked':isLiked(likes,song.id)}" class="fa fa-heart fav"></i>
          </div>
        </li>
      </ul>
    </div>



  </div>
</template>

<script>
import { Ebus } from '../Ebus.js';
import { abbreviate,shorten,isLiked } from "../helpers";
export default {
  name:'feed',
  resource:'Playlist',
  data(){
    return{
      feed:null,
      loading:true
    }
  },
  computed:{
    likes(){
      return this.$store.state.likes;
    }
  },
  props:['playlistId'],
  methods:{
    abbreviate,
    isLiked,
    shorten,
     getBgImg(src){
      return { backgroundImage: `url(${src})` }
    },
    likeSong(song){
      if(isLiked(this.likes,song.id)){
        this.$store.commit('unlike',song);
      }else{        
        this.$store.commit('likeSong',song);
      }
      
    },
    cue(song){
      Ebus.$emit('newCue', song,false);
    }
  },
  watch:{
    async playlistId(to,from){
      this.loading=true;
      this.feed=null;
      if(to===from) return ;
      this.feed =await this.$getResource('playlist',to);
      
      setTimeout(() => {
      this.loading=false;
      }, 300);
    }
  },
  async beforeMount(){
    this.loading=true;
    this.feed =await  this.$getResource('playlist',this.playlistId);
    this.loading=false;
  }
}
</script>

