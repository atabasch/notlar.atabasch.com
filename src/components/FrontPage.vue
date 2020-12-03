<template>
<div id="app" class="container-fluid">
<div class="row">

<nav class="d-none d-sm-block col-sm-12 p-0 m-0" aria-label="breadcrumb">
    <ol id="breadcrumb" class="breadcrumb bg-dark m-0">
      <li class="breadcrumb-item" v-for="(link, index) in links" :key="index">
        <a href="#" v-if="index<links.length-1" @click.prevent="getContents(link, 'parent')">{{ link.name }}</a>
        <span v-else>{{ link.name }}</span>
        </li>
    </ol>
</nav>



<nav class="d-block d-sm-none col-12 p-0 r-0 bg-dark">
  <button @click="showPaths=!showPaths" class="btn btn-primary btn-block">Dosya ve Klasörler</button>
</nav>
<aside class="d-sm-block col-sm-4 col-md-3 col-lg-2 bg-light" :class="{'active':showPaths==true}">
  <ul class="nav flex-column">
    <li v-if="links.length>1">
      <a href="" @click.prevent="getContents(links[0], 'parent')"><span>📂</span> ..</a>
    </li>
    <li v-for="(item, index) in items" :key="index">
      <a href="" @click.prevent="getContents(item, 'sub')"><span v-html="getEmoji(item.type)"></span>{{ item.name }}</a>
    </li>
  </ul>
</aside>

<div id="content" class="col-sm-8 col-md-9 col-lg-10 offset-sm-4 offset-md-3 offset-lg-2">
<div id="loader" v-if="loadingFile">
  <img src="/img/loader.gif" alt="">
</div>
<div v-show="contentTitle" id="contentTitle">{{ contentTitle }}</div>
<div v-show="contentTitle" id="file" ref="file"></div>
</div>

</div>
</div>
</template>

<script>
import hljs from 'highlight.js';
import 'highlight.js/styles/monokai-sublime.css';
import { Remarkable } from 'remarkable';

export default {
  name: 'FrontPage',
  data(){return{
    loadingFile: false,
    md: null,
    contentTitle: "",
    items: [],
    links: [{ name: 'Root', type:'dir', url:'https://api.github.com/repos/asw13tr/notlar/contents/' }],
    showPaths: false
  }},

  created(){
    
    this.md = new Remarkable({
      html:         false,        // Enable HTML tags in source
      xhtmlOut:     false,        // Use '/' to close single tags (<br />)
      breaks:       false,        // Convert '\n' in paragraphs into <br>
      langPrefix:   'language-',
      typographer:  false,
      quotes: '“”‘’',
      highlight: function (content) { 
        return hljs.highlightAuto(content).value; 
      }
    });
    this.getContents(this.links[0], "root");

  },

  methods: {
    getEmoji(type){
      return type=="dir"? "📂" : "📃";
    },

    getParentUrl(){
      return this.links.length > 1? this.links[this.links.length-1].url : this.links[0].url;
    },

    getContents(item, depth){
      if(item.type=="dir"){
        this.items = [];
        this.getList(item, depth);
      }else{
        this.getFile(item, "file");
      }
    },

  getList(item, depth){
    if(depth!='root'){ if(depth=="sub"){ this.links.push(item); }else{ 
      this.links.forEach( (link, index) => {
        if(link.url==item.url){
          this.links.splice(index+1, 99);
        }
      } )
    } }

    this.$http.get(item.url).then( response => {
      this.items = response.body;
    });
  },

  getFile(item){
    this.loadingFile = true;
    this.$refs.file.innerHTML = '';
    this.showPaths=false;

    this.$http.get(item.download_url).then( response => {
      this.contentTitle = item.path;
      this.$refs.file.innerHTML = this.md.render(response.body).replace(/<table>/g, '<table class="table table-bordered table-striped table-sm">');
      this.loadingFile = false;
    });
  }

  },


  

  watch: {
    links(){
      console.log(this.links);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

#breadcrumb{
  border-radius: 0px;
  color: #ccc;
  position: fixed;
  left:0;
  right: 0;
  top: 0;
  z-index: 999;
}

#breadcrumb a{
  color: white;
}


aside{
  position: fixed;
  top: 48px;
  left:0px;
  bottom: 0px;
  overflow-x: hidden;
  overflow-y: auto;
  padding: 10px;
  z-index: 998;
}
@media screen and (max-width:576px) {
  aside{ display: none; }
  aside.active{display: block;}
  aside a{padding: 3px; border: 1px solid #e0e0e0;  }
}

aside ul{
  display: block;
  margin-bottom: 100px;
}
aside ul li{
  padding: 2px 0px  ;
}
aside a{
  color: black;
  display: block;
  font-size: 0.9rem;
}


#content{
  padding: 10px 30px 30px;
  margin-top: 48px;
  position: relative;
}

#contentTitle{
  font-size: 1.5rem;
  padding: 0px 0px 15px;
  border-bottom: 1px solid #e0e0e0;
  margin-bottom: 15px;
}

#loader{
  background-color: rgba(255, 255, 255, 0.80);
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  height: calc(100vh - 48px);
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
