<template>
  <div class="container">
      <div class="sort">
            <button class="cart-btn" value="add" v-if="cart.length === 0 && showProduct !== false" disabled>
                Cart&nbsp;&nbsp;{{cartItemCount}}&nbsp;
                <span class="fas fa-cart-plus"></span>
            </button>
            <button class="cart-btn" value="add" @click="showCheckout" v-else>
                Cart&nbsp;&nbsp;{{cartItemCount}}&nbsp;
                <span class="fas fa-cart-plus"></span>
            </button>
      </div>
    <div v-if="showProduct">
      <Lesson :Lessons="lessons" @lessonId="getLessonInfo" @cart="getCart" > </Lesson>      
    </div>    
    <div v-else>
      <Checkout :cart="cart" :lessonInfo="lessonInfo" ></Checkout>      
    </div>
  </div>
</template>

<script>
import Checkout from './components/Checkout.vue'
import Lesson from './components/Lesson.vue'
import axios from 'axios'

export default {
  name: 'App',
  components: {
    Checkout,
    Lesson
},
  data () {
    return {
      showProduct: true,
      lessons: [],
      users: [],
      lessonInfo: [],
      cart: [],
    }
  },

  created () {
    this.getData()
    this.getUsers()
  },
  computed: {
            cartItemCount: function () {
            return this.cart.length;
        },
  },

  methods : {
    async getData () {
      try {
        const response = await axios.get(
          'https://courseworktwo2.herokuapp.com/collection/Lessons/'
        )
        // JSON responses are automatically parsed.
        this.lessons = response.data
        console.log(this.users)
      } catch (error) {
        console.log(error)
      }
    },
    async getUsers () {
      try {
        const response = await axios.get(
          'https://courseworktwo2.herokuapp.com/collection/Users/'
        )
        // JSON responses are automatically parsed.
        this.users = response.data
        console.log(this.users)
      } catch (error) {
        console.log(error)
      }
    },        
    showCheckout() {
      this.showProduct = this.showProduct ? false : true;
    },
    getCart (value) {
      this.cart = value
    },
    getLessonInfo (value) {
      this.lessonInfo = value
    },
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0;
}
    .container {
          .sort {
            .cart-btn {
                padding: 10px 15px;
                border-radius: 5px;
                border: 1px solid rgba(0, 0, 0, 0.15);
                background-color: rgb(255, 255, 255);
                cursor: pointer;
                width: max-content;
                font-size: 16px;
                line-height: 1.5em;
                margin-left: 75%;

            }
            .cart-btn:focus {
                transform: scale(0.9);
            }
            .cart-btn:hover {
                transform: scale(0.9);
            }
          }
    }
</style>
