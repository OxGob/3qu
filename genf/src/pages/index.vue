<template>
  <q-page>
    <q-tabs color="secondary" glossy align="justify" v-model="selectedTab">
        <q-tab slot="title" default name="QDes" icon="subject" label="QDesign"/>
        <q-tab slot="title" name="QDesPos" icon="subject" label="QDesignPost"/>
      <!-- QDes Tab -->
        <q-tab-pane name="QDes">
          <q-card class="bg-cyan-2 q-ma-xl">
            <q-card-title>Form Designer - New Model
              <span slot="subtitle">Design the form. Create the questions. Shape the flow with follow-up questions by ordering as desired.</span>
            </q-card-title>
            <q-card-separator class="q-mb-md q-mt-xl"/>
            <q-card-main>
            <div v-for="(form, fIndex) in forms" :key="form.id">
              <q-field class="q-mb-sm" label="Form Title: " helper="Please enter the title of the form. This IS displayed to the user.">
                <q-input v-model="form.fname" type="text" align="center" clearable />
              </q-field>
              <q-field class="q-mb-sm" label="Form Description: " helper="Please enter a description for the form. This IS displayed to the user.">
                <q-input v-model="form.fDescription" type="text" align="center" clearable />
              </q-field>
              <q-card-separator class="q-mb-md q-mt-xl"/>
           <div v-for="(question, qIndex) in form.questions" :key="question.id">
            <q-btn class="q-mb-md" round size="sm" color="amber" icon="add" @click="addRowQuestions(fIndex)" />
            <q-btn class="q-mb-md q-ml-md" v-show="qIndex !==0" round size="sm" color="blue" icon="remove" @click="remRowQs(fIndex)" />
            <q-field class="q-mb-sm" label="Question: " helper="Please enter a question. This IS displayed to the user.">
              <q-input v-model="question.qtext" type="text" align="center" clearable />
            </q-field>
            <q-field class="q-mb-sm" label="Help: " helper="Please enter a description for any helper label. This IS displayed to the user.">
              <q-input v-model="question.qHelp" type="text" align="center" clearable />
            </q-field>
            <q-field class="q-mb-sm" label="Question ID: " helper="Please enter a the Question ID. This IS NOT displayed to the user and is for INTERNAL use only.">
              <q-input v-model="question.qId" type="number" align="center" clearable />
            </q-field>
             <q-field class="q-mb-sm" label="Default ID: " helper="Please enter the next Question ID (a number) to proceed. To terminate the form, use the reserved keyword FORMEND or leave empty. This IS NOT displayed to the user and is for INTERNAL use only.">
              <q-input v-model="question.nextDefaultId" type="number" align="center" clearable />
            </q-field>
            <q-card-separator class="q-mb-md q-mt-xl"/>
              <div v-for="(answerChoice, aIndex) in question.answerChoices" :key="answerChoice.id">
                <q-btn class="q-mb-md" round size="sm" color="green" icon="add" @click="addAnswerChoices(fIndex, qIndex)" />
                <q-btn class="q-mb-md q-ml-md" v-show="aIndex !==0" round size="sm" color="negative" icon="remove" @click="remRowAns(fIndex, qIndex)" />
                <q-field class="q-mb-sm" label="Answer Text: " helper="Please enter the answer. e.g. Yes or No. This IS displayed to the user.">
                  <q-input v-model="answerChoice.text" type="text" align="center" clearable />
                </q-field>
                 <q-field class="q-mb-sm" label="Answer ID: " helper="Please enter the answer ID. This IS NOT displayed to the user and is for INTERNAL use only." >
                  <q-input v-model="answerChoice.answerId" type="number" align="center" clearable />
                </q-field>
                <q-field class="q-mb-sm" label="Next Question ID: " helper="Please enter the next Question ID (a number) to proceed. To terminate the form, use the reserved keyword FORMEND or leave empty. This IS NOT displayed to the user and is for INTERNAL use only." >
                  <q-input v-model="answerChoice.nextQuId" type="number" align="center" clearable />
                </q-field>
            <q-card-separator class="q-mb-md q-mt-xl"/>
              </div>
           </div>
           </div>
            </q-card-main>
            <div class="row justify-center">
              <q-btn class="q-mb-md" color="red" icon-right="navigate_next" @click="generateForm">Generate the form</q-btn>
            </div>
          </q-card>
        </q-tab-pane>
          <!-- QDesPos Tab -->
        <q-tab-pane name="QDesPos">
            <q-card class="bg-light-blue-2 q-ma-xl">
            <q-card-title>Resulting form
              <span slot="subtitle">View the designed form.</span>
            </q-card-title>
            <q-card-separator class="q-mb-md q-mt-xl"/>
            <q-card-main>
            <!-- Forms  -->
            <div v-for="(form) in forms" :key="form.id">
              <q-field class="q-mb-sm" label="Form Title: " >
                <q-input v-model="form.fname" />
              </q-field>
              <q-field class="q-mb-sm" label="Form Description: " >
                <q-input v-model="form.fDescription" />
              </q-field>
              <q-card-separator class="q-mb-md q-mt-xl"/>
              <!-- Questions -->
              <q-card class="bg-teal-1 q-mt-lg q-mb-md">
              <div v-for="(question, qIndex) in form.questions" :key="question.id">
                <div  v-show="qIndex === indexToShow">
                <q-field class="q-ml-md q-mt-md q-mb-md" label="Question Number: " >
                  <q-input v-model="question.qId" align="center" />
                </q-field>
                <q-field class="q-ml-md q-mt-md q-mb-md" label="Question: " helper="Please read the question carefully." >
                  <q-input v-model="question.qtext" />
                </q-field>
                <q-card-separator class="q-mb-md q-mt-md"/>
                <!-- Answers -->
                  <q-card class="bg-green-2 q-ml-md q-mt-lg q-mb-md q-mr-md">
                  <div v-for="(answerChoice) in question.answerChoices" :key="answerChoice.id">
                    <q-field class="q-ml-md q-mt-md q-mb-md" label="Answer: " >
                      <q-input class="q-mb-md" v-model="answerChoice.text" />
                    </q-field>
                  </div>
                  </q-card>
                  <q-btn class="q-ml-md q-mb-md q-mt-md" icon-right="navigate_next" color="blue-7" label="Next" @click="goNext"/>
                  <q-btn class="q-mr-md q-mb-md q-mt-md float-right" icon-right="done_all" color="red-7" label="Finish" @click="finishForm"/>
                  <q-card-separator class="q-mb-md q-mt-sm"/>
                </div>
              </div>
              </q-card>
            </div>
            </q-card-main>
            <q-btn class="q-ml-md q-mb-md q-mt-md" color="amber-5" icon="navigate_before" @click="goBack">Go Back to Designer</q-btn>
            </q-card>
        </q-tab-pane>
      </q-tabs>
  </q-page>
</template>

<script>

export default {
  data () {
    return {
      selectedTab: 'QDes',
      indexToShow: 0,
      currFIndex: 55,
      currQIndex: 0,
      currAIndex: 0,
      forms: [
        {
          fname: '',
          fDescription: '',
          questions: [
            {
              qtext: '',
              qHelp: '',
              qId: '', // integer
              nextDefaultId: '', // if empty or undefined or keyword, then complete form after this question
              questionType: '',
              answerChoices: [
                {
                  text: '',
                  answerId: '', // e.g. y
                  nextQuId: '' // integer. If empty or undefined or keyword, then complete form after this question
                }
              ]
            }
          ],
          answers:
          [
            {
              questionId: '',
              answerText: '',
              answerId: '',
              timeStamp: ''
            }
          ]
        }
      ]
    }
  },
  methods: {
    addRowQuestions (fIndex) {
      this.forms[fIndex].questions.push({
        qtext: '',
        qHelp: '',
        qId: '',
        nextDefaultId: '',
        questionType: '',
        answerChoices: [
          {
            text: '',
            answerId: '',
            nextQuId: ''
          }
        ]
      })
    },
    addAnswerChoices (fIndex, qIndex) {
      this.forms[fIndex].questions[qIndex].answerChoices.push({
        text: '',
        answerId: '',
        nextQuId: ''
      })
    },
    remRowQs (fIndex) {
      this.forms[fIndex].questions.splice(fIndex, 1)
    },
    remRowAns (fIndex, qIndex) {
      this.forms[fIndex].questions[qIndex].answerChoices.splice(qIndex, 1)
    },
    generateForm () {
      this.selectedTab = 'QDesPos'
      // this.posTest()
    },
    goBack () {
      this.selectedTab = 'QDes'
    },
    posTest () {
      var lenForm = Object.keys(this.forms).length
      var iForm = ''
      var jForm = ''
      var aForm = ''
      for (iForm = 0; iForm < lenForm; iForm++) {
        this.$q.notify('Length of Form: ' + lenForm)
        var lenQ = Object.keys(this.forms[iForm].questions).length
        for (jForm = 0; jForm < lenQ; jForm++) {
          this.$q.notify('Length of Qu: ' + lenQ)
          // To get value for questions, set key to that of qtext which for now is 0. This works for all qs. change it to reflect question text once all is complete
          var objQ = this.forms[iForm].questions[jForm]
          this.$q.notify('OB Nu ' + lenQ + ': ' + objQ[Object.keys(objQ)[0]])
          var lenA = Object.keys(this.forms[iForm].questions[jForm].answerChoices).length
          for (aForm = 0; aForm < lenA; aForm++) {
            this.$q.notify('Length of An: ' + lenA)
            this.$q.notify('Val Ans ' + lenA + ': ' + Object.values(this.forms[iForm].questions[jForm].answerChoices[aForm]))
          }
        }
      }
    },
    showQu () {
      // Show only Q and A according to relevant qID
      // Set value of var indexToShow
    },
    toggleButton () {
      // Depending on reserved keyword in question/answer, show/hide Next/Finish buttons
      // input: Default Id
    },
    goNext () {
      // To add answer Id navigation logic. depending on Default ID or answer ID navigate
      // input: next QID from current Q/Ans || Output: navigation + add to answer object(?)
      // check next question Id from current answer ID --> related to current index
      var nextQId = this.forms[0].questions[0].answerChoices[0].nextQuId
      this.$q.notify('Next Ans Id is : ' + nextQId)
      // this.$q.notify('crrent q ind : ' + this.currFIndex)
    },
    finishForm () {
      // Button is showed only if keyword in Default ID
      // Output: Saves to answer object. Closes form.
    }
  }
}
</script>
