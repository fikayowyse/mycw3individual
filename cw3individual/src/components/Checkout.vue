<template>
  <div class="container">
        <div class="bg"></div>
    <div class="checkout">
                <div class="section1">
                    <div v-for="lesson in cart"  :key="lesson.id" class="list">
                        <img v-bind:src="lesson.image" alt="img" class="image">
                        <h6>Subject: {{lesson.subject}}</h6>
                        <h6>Location: {{lesson.location}}</h6>
                        <h6 id="spaces">numberPurchased: {{lesson.numberPurchased}}</h6>
                        <h6>Price: {{lesson.price}}.00</h6>
                        <button class="btn" @click="removeCourseFromCart(lesson)">Remove</button>
                </div>
                </div>
                <div class="submit">
                    <label for="name">Name:</label>
                    <input type="text" name="name" id="name" v-model.trim="checkout.Name" />
                    <label for="phone-number">Phone Number:</label>
                    <input type="text" name="phone-number" id="phone-number" v-model="checkout.Phone_No" />
                    <button type="submit" class="submitBtn" id="btnSub" @click="isNumber">Checkout</button>
                </div>
            </div> 
           
  </div>
</template>

<script>

import axios from 'axios'

export default {
  props: {
      cart:[
          Array
      ],
      lessonInfo:[
          Array
      ],
      
  },
  data () {
    return {
        checkout: {
            Name: '',
            Phone_No: null,
            lessonId: []

        } , 
        currentUserId: ''

    }
  },
  created () {

  },
  computed: {
  },

  mounted: function () {
      this.getData () 
      this.checkout.lessonId = this.lessonInfo
  },
    methods: {
        async getData () {
        try {
            const response = await axios.get(
            'https://courseworktwo2.herokuapp.com/collection/Users/6232695c020473916b268f00'
            )
            // JSON responses are automatically parsed.
            this.currentUserId = response.data
        } catch (error) {
            console.log(error)
        }
        },
        //remove a course from the cart
        removeCourseFromCart(lesson) {
            this.cart.pop(lesson); 
            lesson.numberPurchased--  
            this.checkout.lessonId.pop(lesson._id)
            this.checkout.lessonId.pop(lesson.numberPurchased)
            ++lesson.spaces 
        },
        //checks if the details submitted for the name and number are accurate
        async isNumber() {
            let phoneNumber = document.getElementById("phone-number")
            let name = document.getElementById("name")
            let submitBtn = document.querySelector(".submitBtn")
            var integers = /^[0-9]+$/
            var letters = /^[A-Za-z]+$/
            if (phoneNumber.value.match(integers) && name.value.match(letters)) {
                alert('Your Registration number has accepted....');

                if( this.checkout.Name != '' && this.checkout.Phone_No != null){
                    if(this.checkout.Name === this.currentUserId.Name){
                        await axios.put(
                            'https://courseworktwo2.herokuapp.com/collection/Users/' + this.currentUserId._id, {
                                body: this.checkout
                            })
                        alert("Order Updated successfully!")
                    }
                    else{    
                        console.log("posting")     
                            await axios.post(
                            'https://courseworktwo2.herokuapp.com/collection/Users/', {
                                body: this.checkout
                            })
                            alert("Order placed successfully!")          
                    }
                }
                window.location.href = "/index.html"

                return true;
            }
            else {
                alert('Please input only numeric characters for Phone number and letters for Name');

                return false;
            }
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

        .bg{                    
            opacity: 0.6;
            background-color: rgb(212, 212, 212);
            position: absolute;
            top: -10%;
            height: 110vh;
            position: fixed;
            width:100vw;
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
                    background-color: white;
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
                border: 1px solid rgba(0, 0, 0, 0.15);
                background-color: white;
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
                background-color: rgba(23, 47, 58, 0.9);
                box-shadow: 15px 15px 5px rgb(98, 98, 98);
                border-radius: 20px;
                width: auto;
                color: white;
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
