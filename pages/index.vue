<template>
  <div class="container">
    <Layout>
      <Header>
        <h1>Double up</h1>
        <a-alert
          message="Money is a doubles game"
          description="This tool helps you find out how many times you need to double your money to reach your goal"
          type="info"
          closable
          show-icon
        />
        
      </Header>
      <Content>
        <!-- Inputs -->
        <a-row>
          <a-col :span="12">
            <div class="d-flex flex-column form-control">
              <label for="startingAmount">Starting Amount</label>
              <a-input-number class="w-100" id="startingAmount" v-model="startingAmount" :min="1" :formatter="formatNumber"/>
            </div>
          </a-col>
          <a-col :span="12">
            <div class="d-flex flex-column form-control">
              <label for="targetAmount">My Goal</label>
              <a-input-number class="w-100" id="targetAmount" v-model="targetAmount" :min="1" :formatter="formatNumber"/>
            </div>
          </a-col>
        </a-row>

        <!-- Outputs -->
        <template v-if="!needsDouble && amountsAreValid()">
          <a-alert message="Looks like you already made it to your goal. Congrats!" type="success" show-icon />
        </template>
        <div v-else-if="doubles && doubles.length">
          <!-- Looks like we found some doubles -->
          <a-alert :message="`${doubles.length} doubles needed`" :description="`At the end of the doubles, you will have ${finalAmount.toLocaleString()} (Your goal: ${targetAmount.toLocaleString()})`" type="info" />

          <div class="form-control">
          <hr>
            <h2 class="font-weight-light">Doubles ({{doubles.length}})</h2>
          </div>

          <!-- Print each double -->
          <a-card size="small" :title="`Double number ${i+1}`" style="text-align:center; margin: 1rem auto;" v-for="(double,i) in doubles" :key="`double-${i}`">
            <a-row>
              <a-col :span="11">
                  <h2><small>{{currency}}</small> {{double.start}}</h2>
              </a-col>
              <a-col :span="2">to</a-col>
              <a-col :span="11">
                  <h2><small>{{currency}}</small> {{double.end}}</h2>
              </a-col>
            </a-row>
          </a-card>
        </div>
        <div v-else style="margin: 2rem">
          <!-- Something went wrong while trying to compute the value -->
          <a-alert message="Aww shucks, seems we couldn't compute that. Beep boop. Sorry." type="warning" show-icon v-if="!needsDouble">
            <a-button type="primary" @click="clearInputs">
              Fix it
            </a-button>
          </a-alert>
        </div>
      </Content>
      <Footer style="margin-top:2.5rem;">&copy; 2020 - <a href="https://allanjeremy.com">Allan Jeremy</a></Footer>
    </Layout>
  <!-- Explanation  -->



  </div>
</template>

<script>
export default {
  data(){
    return {
      currency: 'KES',
      startingAmount: null,
      targetAmount: null,
      finalAmount: null
    };
  },
  computed: {
    startingAmountString(){
      return this.startingAmount ? parseFloat(this.startingAmount).toLocaleString() : '';
    },
    targetAmountString(){
      return this.targetAmount ? parseFloat(this.targetAmount).toLocaleString() : '';
    },
    needsDouble(){
      return this.startingAmount < this.targetAmount;
    },
    doubles(){
      if(!this.amountsAreValid()) return false;

      let currentAmount = this.startingAmount;
      let doubles = [];
      let doubledAmount;

      // Double until we reach our goal
      while(currentAmount < this.targetAmount){
        doubledAmount = currentAmount * 2;

        doubles.push({
          start: currentAmount.toLocaleString(),
          end: doubledAmount.toLocaleString()
        });

        currentAmount = doubledAmount;
      }

      //! Code smell - Ideally extract this into its own function (Functional programming would be ideal - no side-effects)
      this.finalAmount =  doubledAmount;

      // Return all the doubles we found
      return doubles;
    }
  },
  methods: {
    /** Checks if the amounts are numbers and valid. Returns true if both the numbers are valid */
    amountsAreValid(){
      return (!isNaN(this.startingAmount) && !isNaN(this.targetAmount));
    },
    clearInputs(){
      this.startingAmount = null;
      this.targetAmount = null;
    },
    formatNumber: value => `${value}`.replace(/\B(?=(\d{3})+(?!\d))/g, ','),
  }
}
</script>

<style >
.container {
  margin: 0 auto;
  padding: 1.25rem;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}


h2>small{
    opacity: 0.8;
    font-weight: 300;
  }


.font-weight-light {
  font-weight: 300;
  line-height:150%;
}

label {
  text-align: left;
  margin-bottom: 0.5rem;
}

hr {
  margin-bottom:1rem;
  opacity: 0.2;
}

.w-100 {
  width: 100%;
}

.form-control {
  display: block;
  padding: 0.5rem;
  margin: 1rem 0;
}

/* Flexbox */
.d-flex {
  display: flex !important;
}

.flex-column {
  flex-direction: column !important;
}
</style>
