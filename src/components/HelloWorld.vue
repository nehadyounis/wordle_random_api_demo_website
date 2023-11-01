<template>
  <v-container class="fill-height">
    <v-responsive class="align-center text-center fill-height py-16">
      <v-progress-circular class = "py-16" v-if="loading" indeterminate :size="74" :width="10"></v-progress-circular>
      <h1 v-else class="text-h2 py-16 font-weight-bold"> {{word}}</h1>
      <h2 class="text-h5 font-weight-bold">Or set the parametes to suit your needs</h2>

      <v-btn class = "ma-4" size="x-large" color="primary" @click="call">Generate</v-btn>

      <v-container>
          <v-text-field  @input="buildQuery" readonly v-model="query" ref="textToCopy" placeholder="Your query will appear here"></v-text-field>
          <v-btn @click="copyText">Copy Query</v-btn>
        </v-container>

      <div class="py-14" />
      <v-row class="d-flex justify-center">
        <v-col cols="6">
          <h2 class="text-h4 ma-8 font-weight-bold">Basic Settings</h2>
          <v-text-field @input="buildQuery" v-model="attrs.amount" clearable hide-details="auto" label="Amount" placeholder="How many words to generate per call? max is 25"></v-text-field> 
          <v-switch @change="buildQuery" v-model="attrs.show_info" class='my-4' color="primary" label="Show information about the selected word such as lenght, type ...etc"></v-switch>
        </v-col>
        <v-col cols="6">
            <!-- <h2 class="text-h4 ma-8 font-weight-bold">Filter by common use</h2>
          <v-switch color="primary" label="Return only common words"></v-switch>
          <v-switch color="primary" label="Return only uncommon words"></v-switch> -->
        </v-col>
      </v-row>

      <br><br>


      <v-row class="d-flex justify-center">
        <v-col cols="6">
          <h2 class="text-h4 ma-8 font-weight-bold">Filter by type:</h2>
            <v-combobox @update:menu="buildQuery" v-model="attrs.type" label="Combobox" :items="typesList"></v-combobox>
        </v-col>
        <v-col cols="6">
            <h2 class="text-h4 ma-8 font-weight-bold">Filter by length</h2>
            <v-row class="d-flex justify-center">
              <v-col cols="4">
                 <v-text-field @input="buildQuery" v-model="attrs.len" clearable hide-details="auto" label="Exact length" placeholder="Exact lenght"></v-text-field> 
              </v-col>
               <v-col cols="4">
                 <v-text-field @input="buildQuery" v-model="attrs.min_len" clearable hide-details="auto" label="Minimum length" placeholder="Minimum lenght"></v-text-field> 
              </v-col>
               <v-col cols="4">
                 <v-text-field @input="buildQuery" v-model="attrs.max_len" clearable hide-details="auto" label="Maximum length" placeholder="Maximum lenght"></v-text-field> 
              </v-col>
            </v-row>
        </v-col>
      </v-row>
      <br><br>

      <h2 class="text-h4 ma-8 font-weight-bold">Filter by letters</h2>
        <v-row class="d-flex justify-center">
          <v-col cols="6">
              <v-text-field @input="buildQuery" v-model="attrs.contains_all" clearable hide-details="auto" label="Contains all" placeholder="The returned word(s) should contain all the letters you add here"></v-text-field> 
          </v-col>
            <v-col cols="6">
              <v-text-field @input="buildQuery" v-model="attrs.contains_only" clearable hide-details="auto" label="Contains only" placeholder="The returned word(s) should contain only the letters you add here"></v-text-field> 
          </v-col>
            <v-col cols="6">
              <v-text-field @input="buildQuery" v-model="attrs.contains_any" clearable hide-details="auto" label="Contains none" placeholder="The returned word(s) should contain none of the letters you add here"></v-text-field> 
          </v-col>
          <v-col cols="6">
              <v-text-field @input="buildQuery" v-model="attrs.contains_none" clearable hide-details="auto" label="Contains any" placeholder="The returned word(s) should contain any of the letters you add here"></v-text-field> 
          </v-col>
        </v-row>

        <br><br>

      <h2 class="text-h4 ma-8 font-weight-bold">Filter by Pattern</h2>
        <v-row class="d-flex justify-center">
          <v-col cols="12">
              <v-text-field @input="buildQuery" v-model="attrs.pattern" clearable hide-details="auto" label="Pattern" placeholder="Insert a pattern like li*e to get results similar to [like, live, life, line]"></v-text-field> 
          </v-col>
        </v-row>
        
        <br><br>

        <h2 class="text-h4 ma-8 font-weight-bold">Filter by Regex</h2>
        <v-row class="d-flex justify-center">
          <v-col cols="12">
              <v-text-field @input="buildQuery" v-model="attrs.reg" clearable hide-details="auto" label="Regex" placeholder="Insert a valid regex"></v-text-field> 
          </v-col>
        </v-row>
      



    </v-responsive>
  </v-container>
</template>

<script setup>
import { ref, reactive, watch } from 'vue'
const typesList = [
          "CC coordinating conjunction",
          "CD cardinal digit",
          "DT determiner",
          "EX existential there",
          "FW foreign word",
          "IN preposition/subordinating conjunction",
          "JJ adjective (large)",
          "JJR adjective, comparative (larger)",
          "JJS adjective, superlative (largest)",
          "LS list item marker",
         " MD modal (could, will)",
          "NN noun, singular",
          "NNS noun plural",
          "NNP proper noun, singular",
          "NNPS proper noun, plural",,
          "PDT predeterminer",
          "POS possessive ending (parent\ 's)",
          "PRP personal pronoun (hers, herself, him, himself)",
          "PRP dollar-sign possessive pronoun (her, his, mine, my, our )",
          "RB adverb (occasionally, swiftly)",
          "RBR adverb, comparative (greater)",
          "RBS adverb, superlative (biggest)",
          "RP particle (about)",
          "SYM symbol",
          "TO infinite marker (to)",
          "UH interjection (goodbye)",
          "VB verb (ask)",
          "VBG verb gerund (judging)",
          "VBD verb past tense (pleaded)",
          "VBN verb past participle (reunified)",
          "VBP verb, present tense not 3rd person singular(wrap)",
          "VBZ verb, present tense with 3rd person singular (bases)",
          "WDT wh-determiner (that, what)",
          "WP wh- pronoun (who)",
          "WP dollar-sign possessive wh-pronoun",
          "WRB wh- adverb (how)"
          ]
const generated =  ref(false)
const loading =  ref(false)
const query = ref("https://wordle-random-api.onrender.com/v1/random")
const baseQuery = "https://wordle-random-api.onrender.com/v1/random"
const word = ref('Click to generate a random word!')
const attrs = reactive({
          amount: null,
          show_info: null,
          len: null,
          min_len: null,
          max_len: null,
          common_only: null,
          uncommon_only: null,
          type: null,
          contains_any: null,
          contains_all: null,
          contains_only: null,
          contains_none: null,
          pattern: null,
          reg: null,
        })


const buildQuery = ()=> {


  if(attrs.type) attrs.type = attrs.type.split(' ')[0]

  var list_of_attrs = [];
  for (const prop in attrs) {

    if(prop == 'show_info' && attrs[prop]) attrs.show_info = 1
    if(prop == 'common_only' && attrs[prop]) attrs.common_only = 1
    if(prop == 'uncommon_only' && attrs[prop]) attrs.uncommon_only = 1

    if (attrs[prop] && attrs[prop] != '' && attrs[prop]!=0)
      list_of_attrs.push(String(prop) + '=' + String(attrs[prop]))

    if(prop == 'show_info' && attrs[prop]) attrs.show_info = true
    if(prop == 'common_only' && attrs[prop]) attrs.common_only = true
    if(prop == 'uncommon_only' && attrs[prop]) attrs.uncommon_only = true
  }
  if (list_of_attrs.length > 0)
  query.value = baseQuery + '?' + list_of_attrs.join('&')
  else
  query.value = baseQuery
}

const copyText = () => {
      navigator.clipboard.writeText(query.value);
    }


const call = async()=>{
  loading.value = true
const response = await fetch(query.value, {
  method: "GET",
  headers: {
    'Content-Type': 'application/json',
  }
});
const data = await response.json();
if (data.length == 0)
   word.value = "Oops! something looks wrong with your parameters."
else
  word.value = data.map(x => x.word).join(', ')
loading.value = false

}
</script>
