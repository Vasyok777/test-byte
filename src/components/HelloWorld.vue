<script setup>
   import { reactive, computed, ref } from 'vue';
   const isPrice = ref('1') 
   const isFree = ref('3')
   const doubleWidth = 4;
   const price = reactive({
      blackbalaze: {
         minPay: 7.00,
         priceSrorage: 0.005,
         priceTransfer: 0.01,
      },
      bunny: {
         minPay: 0,
         maxPay: 10.00,
         isStorage: {
            priceOne: 0.01,
            priceTwo: 0.02
         },
         isTransfer: {
            priceOne: 0.01,
            priceTwo: 0.01,
         },
      },
      scaleway: {
         minPay: 0,
         maxPay: Infinity,
         freePay:  75,
         isStorage: {
            priceOne: 0.06,
            priceTwo: 0.03
         },
         isTransfer: {
            priceOne: 0.02,
            priceTwo: 0.02,
         },
      },
      vult: {
         minPay: 5,
         priceSrorage: 0.01,
         priceTransfer: 0.01,
      }
   })
   console.log(price.scaleway.maxPay);
   const storageData = reactive({
      min: 0,
      max: 1000,
      currentValue: 100
   })
   const transferData = reactive({
      min: 0,
      max: 1000,
      currentValue: 400
   })
   const resultBackblaze = computed(()=>{
      let amountStorage = storageData.currentValue * price.blackbalaze.priceSrorage;
      let amountTransfer = transferData.currentValue * price.blackbalaze.priceTransfer;
      let result = (amountStorage + amountTransfer).toFixed(2);
      if(result<price.blackbalaze.minPay) return price.blackbalaze.minPay
      return result
   })
   const resultBunny = computed(()=>{
      let amountStorage;
      let amountTransfer;
      let result;
      if(isPrice.value === '1'){
         amountStorage = storageData.currentValue * price.bunny.isStorage.priceOne;
      } else if(isPrice.value === '2'){
         amountStorage = storageData.currentValue * price.bunny.isStorage.priceTwo;
      }
      if(isPrice.value === '1'){
         amountTransfer = transferData.currentValue * price.bunny.isTransfer.priceOne;
      } else if(isPrice.value === '2'){
         amountTransfer = transferData.currentValue * price.bunny.isTransfer.priceTwo;
      }
      result = (amountStorage + amountTransfer).toFixed(2)
      if(result > price.bunny.maxPay) return price.bunny.maxPay
      return result
   })
   const resultScaleway = computed(()=>{
      let amountStorage = 0;
      let amountTransfer= 0;
      let result;
      if((price.scaleway.freePay - storageData.currentValue) > 0 && (price.scaleway.freePay - transferData.currentValue) > 0) {
         return 0}
      if((storageData.currentValue - price.scaleway.freePay) >= 0 && isFree.value === '3'){
         amountStorage = (storageData.currentValue - price.scaleway.freePay) * price.scaleway.isStorage.priceOne;
      } else if((storageData.currentValue - price.scaleway.freePay) >= 0 && isFree.value === '4'){
         amountStorage = (storageData.currentValue - price.scaleway.freePay) * price.scaleway.isStorage.priceTwo;
      }
      if((transferData.currentValue - price.scaleway.freePay) >= 0 && isFree.value === '3'){
         amountTransfer = (transferData.currentValue - price.scaleway.freePay) * price.scaleway.isTransfer.priceOne;
      } else if((transferData.currentValue - price.scaleway.freePay) >= 0 && isFree.value === '4'){
         amountTransfer = (transferData.currentValue - price.scaleway.freePay) * price.scaleway.isTransfer.priceTwo;
      }
      //if(storageData.currentValue+transferData.currentValue)
      //if(isFree.value === '3'){
      //   amountStorage = (storageData.currentValue - price.scaleway.freePay) * price.scaleway.isStorage.priceOne;
      //} else if(isFree.value === '4'){
      //   amountStorage = (storageData.currentValue - price.scaleway.freePay) * price.scaleway.isStorage.priceTwo;
      //}
      //if(isFree.value === '3'){
      //   amountTransfer = (transferData.currentValue - price.scaleway.freePay) * price.scaleway.isTransfer.priceOne;
      //} else if(isFree.value === '4'){
      //   amountTransfer = (transferData.currentValue - price.scaleway.freePay) * price.scaleway.isTransfer.priceTwo;
      //}
      result = (amountStorage + amountTransfer).toFixed(2)
      return result
   })
   const resultVult = computed(()=>{
      let amountStorage = storageData.currentValue * price.vult.priceSrorage;
      let amountTransfer = transferData.currentValue * price.vult.priceTransfer;
      let result = (amountStorage + amountTransfer).toFixed(2);
      if(result <  price.vult.minPay) return price.vult.minPay
      return result
   })

   const diagramWidth1 = computed(()=>{
      return {
         width: `${resultBackblaze.value * doubleWidth}px`
      }
   })
   const diagramWidth2 = computed(()=>{
      return {
         width: `${resultBunny.value * doubleWidth}px`
      }
   })
   const diagramWidth3 = computed(()=>{
      return {
         width: `${resultScaleway.value * doubleWidth}px`
      }
   })
   const diagramWidth4 = computed(()=>{
      return {
         width: `${resultVult.value * doubleWidth}px`
      }
   })
</script>

<template>
  <div class="container">
   <div class="card">
      <div class="card__top">
         <label class="card__top-label" for="">
            Storage: {{ storageData.currentValue }}
            <input
               type="range"
               :max="storageData.max"
               :value="storageData.currentValue"
               @input="storageData.currentValue = $event.target.value"
            >
         </label>
         <label for="" class="card__top-label">
            Transfer: {{ transferData.currentValue }}
            <input
               type="range"
               :max="transferData.max"
               :value="transferData.currentValue"
               @input="transferData.currentValue = $event.target.value"
            >
         </label>
      </div>
      <div>
      <div class="card__diagrams">
         <div class="card__diagram">
            <div class="card__diagram-title">
               <h4>backblaze</h4>
            </div>
           <div class="diagram__box">
            <div class="diagram" :style="diagramWidth1"></div> 
            <span>{{ resultBackblaze }}$ (max: {{ price.blackbalaze.minPay }}$)</span>
           </div>
         </div>
      </div>
      <div class="card__diagrams">
         <div class="card__diagram">
            <div class="card__diagram-title">
               <h4>bunny.net</h4>
               <div class="card__diagram-checbox">
                  <label for="idr1" class="label">
                     <input type="radio" name="radio" value="1" id="idr1" v-model="isPrice">
                     HHD
                  </label>
                  <label for="idr2" class="label">
                     <input type="radio" name="radio" value="2" id="idr2" v-model="isPrice">
                    SSD
                  </label>
               </div>
            </div>
           <div class="diagram__box">
            <div class="diagram"  :style="diagramWidth2"></div> 
            <span>{{ resultBunny }}$ (max: {{ price.bunny.maxPay }}$)</span>
           </div>
         </div>
      </div>
      <div class="card__diagrams">
         <div class="card__diagram">
            <div class="card__diagram-title">
               <h4>scaleway.com</h4>
               <div class="card__diagram-checbox">
                  <label for="idr3" class="label">
                     <input type="radio" name="radio1" value="3" id="idr3" v-model="isFree">
                     Multi 
                  </label>
                  <label for="idr4" class="label">
                     <input type="radio" name="radio1" value="4" id="idr4" v-model="isFree">
                     Single 
                  </label>
               </div>
            </div>
           <div class="diagram__box">
            <div class="diagram"  :style="diagramWidth3"></div> 
            <span>{{ resultScaleway }}$ (max: {{ price.bunny.maxPay }}$)</span>
           </div>
         </div>
      </div>
      <div class="card__diagrams">
         <div class="card__diagram">
            <div class="card__diagram-title">
               <h4>vult</h4>
            </div>
           <div class="diagram__box">
            <div class="diagram" :style="diagramWidth4"></div> 
            <span>{{ resultVult }}$ (max: {{ price.vult.minPay }}$)</span>
           </div>
         </div>
      </div>
   </div>
   </div>
  </div>
</template>


<style lang="scss">
html {
  font-family: 'Roboto', sans-serif;
  color: #474747;
}

h1,
h2,
h3,
h4 {
  font-weight: 700;
  color: #333333;
  margin-top: 0;
}

.flex {
  display: flex;
}

.list-default {
  padding-left: 0;
  margin-bottom: 0;
  list-style: none;
  li {
    font-size: 16px;
    margin-bottom: 14px;
  }
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 20px;
  margin-top: 40px;
}

   *{
      margin: 0;
      padding: 0;
      box-sizing: border-box;
   }
   .container {
      max-width: 1200px;
      margin: 0 auto;
   }
   .card{
      border: 1px solid #474747;
      width: 800px;
      margin: 0 auto;
      border-radius: 20px;
      margin-top: 20px;
      padding: 20px;
      &__top{
         display: grid;
         grid-template-columns: repeat(2, 300px);
         grid-gap: 50px;
         margin-bottom: 20px;
      }
      &__diagram{
         display: flex;
         align-items: center;
         gap: 10px;
         &-title{
            width: 200px;
         }
         &-checbox{
            display: flex;
            gap: 10px;
         }

      }
   }
   .label{
      display: flex;
      font-size: 10px;
   }
   .diagram{
      display: flex;
      justify-content: space-between;
      width: 100px;
      height: 20px;
      gap: 10px;
      background: red;
      &__box{
         padding: 20px 0;
         padding-left: 10px;
         display: flex;
         gap: 10px;
         position: relative;
      }
      &__box::after{
         content: '';
         position: absolute;
         width: 10px;
         top: 0;
         left: 0;
         bottom: 0;
         background-color: black;
      }
   }
   h4{
      margin-bottom: 5px;
   }
   .card__top-label{
      display: flex;
      flex-direction: column;

   }
</style>