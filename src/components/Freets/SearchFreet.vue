<template>
    <form class="search-freet-form" @submit.prevent="onSubmit">
      <h3 class="title">Search for a Freet</h3>
      <input 
        class="search-container" 
        id="search" 
        placeholder='search for a freet by author....'
        v-model="searchContent">
      <input class="button" type="submit" value="Search">  
        
      <div><span class='error-msg' v-if="error">{{ error }}</span>
      <Freet 
        v-else-if="numResults!=0"
        v-for="freet in results"
        :key="freet.id"
        :freet="freet"
        :search="true"
        />
      <span class='error-msg' v-else>This user has no freets posted yet.</span></div>
    </form>
  


</template>

<script>
import axios from 'axios'
import Freet from '@/components/Freets/Freet.vue'
import { eventBus } from '../../main'
export default ({
    name: 'SearchFreet',
    components: { Freet },
    data(){
        return{
            searchContent: '',
            results: null,
            error: null,
            numResults: null
        }
    },
    methods:{
        onSubmit(){
          this.error=null,
          axios.get("/api/freets/show/" + this.searchContent)
            .then((response) => {
              if(response.status === 200){
                eventBus.$emit("search-freet-success",{
                    data: response.data,
                });
                this.results = response.data;
                
              }
            })
              .catch((error) => {
                if (error.response && error.response.status != 200){
                  this.error = error.response.data.error
                }
              }).then(() => {
                this.searchContent='';
                this.numResults = this.results.length;
                })
          
        },
    
  
  
    }
})
</script>
<style scoped>
.search-freet-form{
  height: 280px;
  display: flex;
  flex-direction: column;
  width: calc(100% - 120px);
  padding: 20px;
  margin: 40px ;
  background-color: white;
  border-radius: 10px;
  box-shadow: 1px 1px 6px 0 rgba(31,38,135,0.3);
  overflow: auto;
}
.title {
  background: -webkit-linear-gradient(120deg, #00E5FF, #1200FF);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}
.search-container {
  display: flex;
  height: 100px;
  padding: 20px;
  border-radius: 10px;
  background-color: #E8F0FC;
  border: 0;
}
.button {
  font-weight: 600;
  border: none;
  margin-top: 20px;
  color: white;
  padding: 10px 20px;
  border-radius: 50px;
  background: -webkit-linear-gradient(120deg, #00E5FF, #1200FF);
  box-shadow: 1px 1px 6px 0 rgba(31,38,135,0.3);
}

.button:hover {
    filter: brightness(1.2);
    transition: All 0.2s ease-out;
    cursor: pointer;
}

.error-msg {
  margin-left:40px;
  color: rgb(0, 0, 0);
}


</style>