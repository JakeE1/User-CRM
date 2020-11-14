<template>
    <div class="wrapper-content wrapper-content--fixed">
    <section>
      <div class="container">
        
        <table>
          <thead>
            <tr>
              <th @click="sort('name')">Name &#8595;</th>
              <th @click="sort('age')">Age</th>
              <th @click="sort('gender')">Gender</th>
            </tr>
          </thead>

          <tbody>
            <tr v-for="user in usersSort" :key="user.id">
             <td>
               <img :src="user.img" :alt="user.name">
               <span>{{user.name}}</span>
               </td>
             <td>{{user.age}}</td>
             <td>{{user.gender}}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </section>

     <!-- buttons -->
    <section> 
      <div class="container">
        <div class="button-list">
          <div class="btn btnPrimary" @click="prevPage">&#171;</div>
           <div class="counter"> {{this.page.current}} </div>
          <div class="btn btnPrimary" @click="nextPage">&#187;</div>
        </div>  
      </div>
    </section>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data () {
    return {
      users: [],
      currentSort: 'name',
      currentSortDir: 'asc',
      page: {
        current: 1,
        length: 4
      } 
    }
  },
  created() {
    axios
      .get('http://localhost:3000/users')
        .then(response => {
          this.users = response.data
        })
        .catch(error => {
          console.log(error)
        })
  },
  computed: {
    usersSort () {
      return this.users.sort((a, b) => {
        let mod = 1
        if (this.currentSortDir === 'desc') mod = -1
        if (a[this.currentSort] < b[this.currentSort]) return -1 * mod
        if (a[this.currentSort] > b[this.currentSort]) return 1 * mod
        return 0
      }).filter((row, index) => {
        let startIndex = (this.page.current - 1) * this.page.length
        let endIndex = (this.page.current) * this.page.length
        if (index >= startIndex && index < endIndex) return true 
      })
    }  
  },
  methods: {
    sort (e) {
      if (e === this.currentSort) { 
        this.currentSortDir = this.currentSortDir === 'asc' ? 'desc' : 'asc'
      }
      this.currentSort = e
    },
    prevPage () {
      if(this.page.current > 1) this.page.current-=1
    }, 
    nextPage() {
      if ((this.page.current * this.page.length) < this.users.length) this.page.current+=1
    }

  }
  
}
</script>

<style lang="scss" scoped>
img {
  width: 60px;
  height: auto;
  border-radius: 50%;
  margin-right: 15px;
}

.button-list {
  width: 100%;
  display: flex;
  justify-content: center;

  .btn { 
    border-radius: 60px;
    margin: 0 20px;
  }
}

.counter {
  padding: 10px 0;
  margin: 0 10px;
}
</style>

