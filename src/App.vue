<template>
  <main>
    <h1>Prioritizer</h1>
    <!-- START -->
    <div v-if="currentView == 'start'">
      <h2>Setup</h2>
      <p>Note: Data is not stored even in the browser, a refresh will clear data</p>
      <label>Replace with your question</label>
      <br/>
      <input type="text" v-model="criteria" placeholder="Which is a higher priority?">
      <div class="textarea-container">
        <textarea v-model="itemsList" placeholder="Enter items separated by line breaks">
        </textarea>
      </div>
      <button @click="changeMode('sort')">Prioritize</button>
      <button @click="changeMode('bucket')">Bucket</button>
      <button @click="changeMode('final')">Final</button>
    </div>

    <!-- SORT VIEW -->
    <div class="sort-container"
      v-if="currentView == 'sort'">
      <h2>Sort</h2>
      <div class="sort-container">
        <p>Criteria: {{ criteria }}</p>
        <div class="card-container" v-if="flashcards.length > 0">
          <div class="card">
            <div class="card-content" 
            @click="this.nextCard">
            {{ flashcards[this.currentIndex] }}
            </div>
          </div>
          <div class="card">
            <div class="card-content"
            @click="swapIndex(this.currentIndex,this.currentIndex + 1)">
            {{ flashcards[this.currentIndex + 1] }}
            </div>
          </div>
        </div>
      </div>
      <div class="list-container">
        <ol>
        <li v-for="(item, index) in flashcards" :key="index">{{ item }}</li>
        </ol>
      </div>
      <button @click="changeMode('start')" >Reset</button>
    </div>
    <!-- END SORT -->

    <!-- BUCKET VIEW -->
    <div class="bucket-container"
      v-if="currentView == 'bucket'">
      <h2>Bucket</h2>
        <div class="card">
          <div class="card-content">Card 1 Content</div>
        </div>
        <input type="text" placeholder="Label"/><button>Apply</button><button>Done</button>
        <br>
      <button @click="changeMode('start')" >Reset</button>
    </div>
    <!-- FINAL VIEW -->

    <div class="final-container"
      v-if="currentView == 'final'">
      <h2>Final</h2>
      {{ final }}
      <button @click="changeMode('start')" >Reset</button>
    </div>

  </main>
</template>

<script>
export default {
  data() {
    return {
      itemsList: '',
      currentView: 'start', // Possible values: 'start', 'sort', 'bucket', 'final'
      criteria: 'Which is a higher priority?',
      flashcards: [
        
      ],
      currentIndex: 0,
      userChoices: [],
      final: [

      ],
    }
  },
  methods: {
    changeMode(mode) {
      if (mode == "sort") {
        this.flashcards = this.initFlashCards(this.itemsList)
        this.stopIndex = this.flashcards.length
        this.currentView = mode
      }
      else if (mode == "bucket") {
        this.currentView = mode
      }
      else {
        this.currentView = mode
      }
      
    },
    quickSort(items) {
      if (items.length <= 1) {
        return items;
      }
      let pivotIndex = Math.floor(items.length / 2);
      let pivot = items.splice(pivotIndex, 1)[0];
      let left = [];
      let right = []

      items.forEach(item => {
        if(this.compare(item, pivot)) {
          left.push(item);
        } else {
          right.push(item);
        }
      });

      return [...this.quickSort(left), pivot, ...this.quickSort(right)];
    },
    nextCard() {
      if (this.done()) {
        this.finalize()
      } else {
        this.incrementIndex()
      }
    },
    swapIndex(a,b){
      if (this.done()) {
        this.finalize() 
      } else {
        let a_value = this.flashcards[a]
        let b_value = this.flashcards[b]

        this.flashcards[b] = a_value
        this.flashcards[a] = b_value
        this.incrementIndex()
      } 
   
    },
    initFlashCards(itemsList) {
      return itemsList.split("\n");
    },
 
    incrementIndex() {
      console.log(`currentIndex: ${this.currentIndex} flashcardsLength: ${this.flashcards.length}`)
      if (this.currentIndex == this.flashcards.length) {
        this.currentIndex = 0
      } else {
        this.currentIndex += 1
      }
    },
    done() {
      if (this.currentIndex >= this.stopIndex) {
        return true
      } else {
        return false
      }
    },
    finalize() {
        this.final = this.flashcards
        this.flashcards = []
        this.mode = 'final'
    }


  }
}
</script>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}

.textarea-container textarea {
  width: 100%; /* Full-width */
  height: 150px; /* Adjustable height */
  padding: 12px 20px; /* Some padding */
  box-sizing: border-box; /* Make sure padding doesn't affect the box's size */
  border: 2px solid #ccc; /* Subtle border */
  border-radius: 4px; /* Rounded corners */
  background-color: #444444; /* Light grey background */
  resize: none; /* Disable resizing */
  font-size: 16px; /* Slightly larger font size */
  color: #ffffff; /* Darker font color for contrast */
  margin-bottom: 10px; /* Margin to separate from buttons */
}

.textarea-container textarea:focus {
  outline: none; /* Remove default focus outline */
  border-color: #9ecaed; /* Light blue border on focus */
  box-shadow: 0 0 10px #9ecaed; /* Soft glow effect */
}

.card-container {
  display: flex; /* Align cards horizontally */
  justify-content: space-around; /* Distribute space around the cards */
  padding: 20px; /* Add some padding around the container */
}

.card {
  background-color: #333; /* Dark background for the card */
  color: #fff; /* Light text color for contrast */
  border-radius: 8px; /* Rounded corners */
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2); /* Subtle shadow for depth */
  width: 45%; /* Each card takes up 45% of the container width */
  transition: 0.3s; /* Smooth transition for hover effects */
  cursor: pointer;
}

.card:hover {
  box-shadow: 0 8px 16px 0 rgba(0,0,0,0.2); /* Enhanced shadow on hover */
  background-color:#5f5f5f;
}

.card-content {
  padding: 16px; /* Padding inside the card */
  /* More styling could be added here for content-specific needs */
}

</style>
