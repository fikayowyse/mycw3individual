<template>
  <div class="container">
        <div class="bg"></div>
        <div class="header" >
            <div class="sort" >
                   <select name="sortBy" id="select" v-model="sortBy" >
                    <option value="subject">Sort by Subject</option>
                    <option value="location">Sort by Location</option>
                    <option value="price">Sort by Price</option>
                    <option value="availability">Sort by Availability</option>
                    </select>
                    <select name="orderby" id="orderBy" v-model="orderBy">
                        <option value="ascending" >Ascending</option>
                        <option value="descending">Descending</option>
                    </select>
                </div>

                <form action="" class="search">
                    <input type="text" v-model="searchValue"  placeholder="Search for a lesson">
                </form>
            <hr >      
        </div>    
        <div>
            <div class="section1">
                <div v-for="lesson in sortLessons" :key="lesson._id" class="list">          
                <img :src="lesson.image" alt="img" class="image">
                  <p><strong>Subject: </strong>{{lesson.subject}}</p>
                  <p><strong>Location: </strong>{{lesson.location}}</p>
                  <p><strong>Price: </strong> {{lesson.price}}.00</p>
                  <p><strong>Available spaces: </strong>{{lesson.spaces}}</p>
                  <button class="btn" @click="addCourseToCart(lesson)" v-if='canAddToCart(lesson)'>Add
                      to Cart</button>
                  <button disabled='disabled' v-else>No more spaces in this class</button>
                  <span v-if="lesson.spacesAvailable === itemsInCart(lesson.id)">
                      Sold out!
                  </span>
                </div>
            </div>
        </div>   
    


  </div>
</template>

<script>


export default {
  props: {
    Lessons: [
      Array
    ]
  },
  data () {
    return {
        sortBy: "subject",  
        orderBy: 'ascending',
        searchValue: '', 
        userInfo: [],
        cart: [],
        lessonInfo: [],
        currentUserId: ''

    }
  },
  mounted: function () {

  },
  methods: {
    addCourseToCart(lesson) {
      if(lesson.spaces > 0){
          --lesson.spaces  
        }            
            for(var i = 0; i< this.Lessons.length; i++){
                if(lesson.id == this.Lessons[i].id){
                  lesson.numberPurchased++
                    this.cart.push(lesson)
                    this.lessonInfo.push("id: " + lesson._id)
                    this.lessonInfo.push("Space(s): " + lesson.numberPurchased)
                    this.$emit('lessonId', this.lessonInfo)
                    this.$emit('cart', this.cart)
                }
            }
    },
        //checks if course can be added to the cart
        canAddToCart(lesson) {
            return lesson.availableInventory > this.itemsInCart(lesson);
        },
        //counts the number of items in the cart
        itemsInCart(id) {
            let count = 0;
            for (let i = 0; i < this.cart.length; i++) {
                if (this.cart[i] === id) {
                    count++
                }
            }
            return count;
        },
  }, 
  computed: {
        //sort lessons in ascending or descending order, or according to lesson properties
        sortLessons() {
            let lessonOrder = this.Lessons;

            lessonOrder = lessonOrder.sort((a, b) => {
                if (this.sortBy == "subject") {
                    let fa = a.subject.toLowerCase(), fb = b.subject.toLowerCase();

                    if (fa < fb) {
                        return -1
                    }
                    if (fa > fb) {
                        return 1
                    }
                }
                else if (this.sortBy == 'location') {
                    let fa = a.location.toLowerCase(), fb = b.location.toLowerCase();

                    if (fa < fb) {
                        return -1
                    }
                    if (fa > fb) {
                        return 1
                    }
                }
                return 0
            })
            if (this.sortBy == 'price') {
                lessonOrder = lessonOrder.sort((a, b) => {
                    return a.price - b.price
                })
            }
            if (this.sortBy == 'availability') {
                lessonOrder = lessonOrder.sort((a, b) => {
                    return a.spaces - b.spaces
                })
            }
            if (this.orderBy !== "ascending") {
                lessonOrder.reverse()
            }
            if (this.searchValue != '' && this.searchValue) {
                lessonOrder = lessonOrder.filter((item) => {
                    return item.subject
                        .toUpperCase()
                        .includes(this.searchValue.toUpperCase())
                })
            }
            return lessonOrder
        },
    }

}
</script>

<style scoped lang="scss">
    .container {
        position: relative;
        margin: 5% auto;
        margin-top: 3%;
        align-items: center;
        width: 100vw;

        .bg{                    
            //background-image: url("bg.jpg");
            opacity: 0.6;
            background-color: rgb(212, 212, 212);
            position: fixed;
            top: -10%;
            height: 140vh;
            width: 99vw;
            z-index: -5;
        }

        .header {
            display: grid;
            grid-template-columns: 1.5fr 2fr .3fr;
            width: 80vw;
            position: relative;
            margin: auto;
            align-items: center;
            justify-content: center;

            .sort {

                select {
                    padding: 10px 10px;
                    border-radius: 5px;
                    border: 1px solid rgba(0, 0, 0, 0.15);
                    background-color: blue;
                    font-size: 16px;
                    margin-right: 5%;
                }
            }
            .search input {
                padding: 10px 10px;
                font-size: 16px;
                line-height: 1.5m;
                width: 80%;
            }
            .cart-btn {
                padding: 10px 15px;
                border-radius: 5px;
                border: 1px solid white;
                background-color: blue;
                cursor: pointer;
                width: max-content;
                justify-self: end;
                font-size: 16px;
                line-height: 1.5em;
            }
            .cart-btn:focus {
                transform: scale(0.9);
            }

            hr {
                grid-column: 1/4;
                width: 100%;
                margin: 2% 0;
            }
        }


        .section1 {
            clear: both;
            position: relative;
            display: grid;
            grid-template-columns: .2fr .2fr .2fr;
            grid-row-gap: 5%;
            grid-column-gap: 5%;
            width: 80vw;
            margin: auto;
            padding-bottom: 10em;
            justify-content: center;
            
            .list {
                padding: 40px 30px;
                background-color: white;
                box-shadow: 15px 15px 5px rgb(98, 98, 98);
                border: 1px solid blue;
                border-radius: 20px;
                width: auto;
                color: black;
            }
                strong {
                     text-transform: uppercase;
                }

            
            .btn {
                bottom: 7%;
                border: none;
                outline: none;
                cursor: pointer;
                width: 100%;
                margin: auto;
                padding: 10px 20px;
                background-color: rgba(32, 114, 221, .9);
                color: white;
                border-radius: 10px;

            }
            .btn:hover {
                transform: scale(.9);
            }

            .image {
                width: 270px;
                height: 200px;
                border-radius: 20px;
            }

        }
    }
</style>
