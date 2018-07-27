<template>
  <q-page>
    <q-tabs color="secondary" glossy align="justify" v-model="selectedTab">
        <q-tab slot="title" default name="QDes" icon="subject" label="QDesign"/>
        <q-tab slot="title" v-if="tabWasLoaded" name="QDesPos" icon="subject" label="QDesignPost"/>
      <!-- QDes Tab -->
        <q-tab-pane name="QDes">
          <q-card class="bg-cyan-2 q-ma-xl">
            <q-card-title>Form Designer - New Model
              <span slot="subtitle">Design the form. Create the questions. Shape the flow with follow-up questions by ordering as desired.</span>
            </q-card-title>
            <q-btn class="q-ml-md" color="amber"  label="Start Again" @click="refreshPage"/>
            <q-card-separator class="q-mb-md q-mt-xl"/>
            <q-card-main>
      <!-- QDes - Forms  -->
            <div v-for="(form, fIndex) in forms" :key="form.id">
              <q-field class="q-mb-sm" label="Form Title: " helper="Please enter the title of the form. This IS displayed to the user.">
                <q-input v-model="form.fname" type="text" align="center" clearable />
              </q-field>
              <q-field class="q-mb-sm" label="Form Description: " helper="Please enter a description for the form. This IS displayed to the user.">
                <q-input v-model="form.fDescription" type="textarea" rows="5" align="center" clearable />
              </q-field>
              <q-btn class="q-mt-sm" label="Add Questions" color="blue" icon="add" @click="addRowQuestions(fIndex)" />
              <q-card-separator class="q-mb-md q-mt-xl"/>
      <!-- QDes - Questions -->
           <div v-for="(question, qIndex) in form.questions" :key="question.id">
            <!-- <q-btn class="q-mb-md" round size="sm" color="amber" icon="add" @click="addRowQuestions(fIndex)" /> -->
            <q-btn class="q-mb-md q-ml-md" v-show="qIndex !==0" round size="sm" color="red-3" icon="remove" @click="remRowQs(fIndex, qIndex)" />
            <q-field class="q-mb-sm" label="Question ID: " helper="This Question ID is automatically generated. This IS NOT displayed to the user and is for INTERNAL use only.">
              <q-input v-model="question.qId" type="number" align="center" readonly />
            </q-field>
            <q-field class="q-mb-sm q-mt-md" label="Question Type: " helper="Please select a question type. This IS NOT displayed to the user and is for INTERNAL use only.">
              <q-select v-model="question.qType" :options="quSelOptions" />
            </q-field>
            <q-field class="q-mb-sm" label="Question: " helper="Please enter a question. This IS displayed to the user.">
              <q-input v-model="question.qtext" type="textarea" rows="6" align="center" clearable />
            </q-field>
            <q-field class="q-mb-sm" label="Help: " helper="Please enter a description for any helper label. This IS displayed to the user.">
              <q-input v-model="question.qHelp" type="text" align="center" clearable />
            </q-field>
             <q-field class="q-mb-sm" label="Default/Next Question ID: " helper="Please enter the next Question ID (a number) to proceed. To terminate the form, use the reserved keyword ENDFORM or leave the field blank. This IS NOT displayed to the user and is for INTERNAL use only.">
              <q-input v-model="question.nextDefaultId" type="text" align="center" clearable />
            </q-field>
            <q-card-separator class="q-mb-md q-mt-xl"/>
      <!-- QDes - Answers -->
            <div  v-show="question.qType !== 'freetext'">
              <div v-for="(answerChoice, aIndex) in question.answerChoices" :key="answerChoice.id">
                <q-btn class="q-mb-md" round size="sm" color="green" icon="add" @click="addAnswerChoices(fIndex, qIndex)" />
                <q-btn class="q-mb-md q-ml-md" v-show="aIndex !==0" round size="sm" color="negative" icon="remove" @click="remRowAns(fIndex, qIndex, aIndex)" />
                <q-field class="q-mb-sm" label="Answer Text: " helper="Please enter the answer. e.g. Yes or No. This IS displayed to the user.">
                  <q-input v-model="answerChoice.text" type="text" align="center" clearable />
                </q-field>
                 <q-field class="q-mb-sm" label="Answer ID: " helper="Please enter the answer ID. This IS NOT displayed to the user and is for INTERNAL use only." >
                  <q-input v-model="answerChoice.answerId" type="number" align="center" onkeypress="return event.charCode >= 48 && event.charCode <= 57" clearable />
                </q-field>
                <q-field class="q-mb-sm" label="Next Question ID: " helper="Please enter the next Question ID (a number) to proceed. To terminate the form, leave the field blank. This IS NOT displayed to the user and is for INTERNAL use only." >
                  <q-input v-model="answerChoice.nextQuId" type="number" align="center" onkeypress="return event.charCode >= 48 && event.charCode <= 57" clearable />
                </q-field>
            <q-card-separator class="q-mb-md q-mt-xl"/>
              </div>
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
          <!-- QDesPos - Forms  -->
            <div v-for="(form) in forms" :key="form.id">
              <q-field class="q-mb-sm" label="Form Title: " >
                <q-input v-model="form.fname" />
              </q-field>
              <q-field class="q-mb-sm" label="Form Description: " >
                <q-input v-model="form.fDescription" />
              </q-field>
              <q-card-separator class="q-mb-md q-mt-xl"/>
          <!-- QDesPos - Questions -->
              <q-card class="bg-teal-1 q-mt-lg q-mb-md">
              <div v-for="(question, qIndex) in form.questions" :key="question.id">
                <div  v-show="qIndex === indexToShow">
                <q-field class="q-ml-md q-mt-md q-mb-md" label="Question Number: " >
                  <q-input v-model="question.qId" align="center" readonly/>
                </q-field>
                <q-field class="q-ml-md q-mt-md q-mb-md" label="Question: " helper="Please read the question carefully." >
                  <q-input v-model="question.qtext" type="textarea" rows="6" align="center" readonly/>
                  {{ question.qHelp }}
                </q-field>
                <q-card-separator class="q-mb-md q-mt-md"/>
          <!-- QDesPos - Answers -->
                  <q-card class="bg-green-2 q-ml-md q-mt-lg q-mb-md q-mr-md">
                  <div  v-show="question.qType !== 'freetext'">
                    <div v-for="(answerChoice) in question.answerChoices" :key="answerChoice.id">
                      <q-field class="q-ml-md q-mt-md q-mb-md" label="Answer: " >
                        <q-input class="q-mb-md" v-model="answerChoice.text" align="center" onkeypress="return event.charCode >= 48 && event.charCode <= 57" clearable/>
                      </q-field>
                    </div>
                  </div>
                  <div  v-show="question.qType === 'freetext'">
                    <div v-for="(answer) in form.answers" :key="answer.id">
                      <q-field class="q-ml-md q-mt-md q-mb-md" label="Answer: " helper="Please write an answer." >
                        <q-input class="q-mb-md" v-model="answer.answerText" align="center" type="textarea" rows="6" clearable/>
                      </q-field>
                    </div>
                  </div>
                  </q-card>
                  <div  v-show="showNextBtn">
                  <q-btn class="q-ml-md q-mb-md q-mt-md" icon-right="navigate_next" color="blue-7" label="Next" @click="goNext"/>
                  </div>
                  <q-card-separator class="q-mb-md q-mt-sm"/>
                </div>
              </div>
              </q-card>
            </div>
             <div  v-show="showFinishBtn">
              <q-btn class="q-mr-md q-mb-md float-right" icon-right="done_all" color="red-7" label="Finish" @click="finishForm"/>
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
      tabWasLoaded: false,
      genQuIdCounter: 0,
      arrayGenQuId: [0],
      trackingID: [
        {
          quesID: 0,
          quesIndex: 0
        }
      ],
      quSelOptions: [
        {
          label: 'Freetext',
          value: 'freetext'
        },
        {
          label: 'Multiple choice',
          value: 'multi'
        },
        {
          label: 'Single choice',
          value: 'single'
        }
      ],
      nextQuIndex: 0,
      indexToShow: 0,
      currFIndex: 0,
      currQIndex: 0,
      currAIndex: 0,
      showNextBtn: true,
      showFinishBtn: false,
      forms: [
        {
          fname: '',
          fDescription: '',
          questions: [
            {
              qtext: '',
              qHelp: '',
              qId: 0, // integer
              nextDefaultId: '', // if empty or undefined or keyword, then complete form after this question
              qType: 'freetext',
              answerChoices: [
                {
                  answerId: 0,
                  text: '',
                  answerValue: '', // e.g. y
                  nextQuId: '' // integer. If empty or undefined or keyword, then complete form after this question
                }
              ],
              answerOptions: [
                {
                  label: '',
                  value: ''
                }
              ],
              genAnsIdCounter: 0
            }
          ],
          answers: [
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
    // Add Remove
    addRowQuestions (fIndex) {
      this.genQuIdCounter++
      this.arrayGenQuId.push(this.genQuIdCounter)
      this.forms[fIndex].questions.push({
        qtext: '',
        qHelp: '',
        qId: this.genQuIdCounter,
        nextDefaultId: '',
        qType: 'freetext',
        answerChoices: [
          {
            answerId: 0,
            text: '',
            answerValue: '', // e.g. y
            nextQuId: '' // integer. If empty or undefined or keyword, then complete form after this question
          }
        ],
        answerOptions: [
          {
            label: '',
            value: ''
          }
        ],
        genAnsIdCounter: 0
      })
      this.addTrackingId(fIndex)
    },
    addAnswerChoices (fIndex, qIndex) {
      this.forms[fIndex].questions[qIndex].answerChoices.push({
        text: '',
        answerId: '',
        nextQuId: ''
      })
    },
    remRowQs (fIndex, qIndex) {
      this.forms[fIndex].questions.splice(qIndex, 1)
      // remove the selected index from the question tracking Array
      this.trackingID.splice(qIndex, 1)
      // In the tracking array, update the question index for those removed AFTER SPLICE
      var lengthOfTrackerAfterSplice = Object.keys(this.trackingID).length
      if (qIndex < lengthOfTrackerAfterSplice) {
      // If qIndex is NOT LAST, then from qIndex position till last, subtract 1 from values of quesIndex
        var i
        for (i = qIndex; i < lengthOfTrackerAfterSplice; i++) {
          var fn = this.trackingID[i].quesIndex - 1
          this.trackingID[i].quesIndex = fn
        }
      }
    },
    remRowAns (fIndex, qIndex, aIndex) {
      this.forms[fIndex].questions[qIndex].answerChoices.splice(aIndex, 1)
    },
    addAnswers () {
      var formIndex = this.currFIndex
      this.$q.notify('the form index is: ' + formIndex)
      var questionIndex = this.currQIndex
      var answerIndex = this.currAIndex
      // To fill in respective answers for each form, use index of questions
      this.forms[formIndex].answers[questionIndex].questionId = this.forms[formIndex].questions[questionIndex].qId
      this.forms[formIndex].answers[questionIndex].answerText = this.forms[formIndex].questions[questionIndex].answerChoices[answerIndex].text
      this.forms[formIndex].answers[questionIndex].answerId = this.forms[formIndex].questions[questionIndex].answerChoices[answerIndex].answerId
      this.forms[formIndex].answers[questionIndex].timeStamp = this.timeStamp1(new Date(), 'en-gb')
      this.forms[formIndex].answers.push({
        questionId: '',
        answerText: '',
        answerId: '',
        timeStamp: ''
      })
    },
    // Navigation Methods
    generateForm () {
      this.selectedTab = 'QDesPos'
      this.tabWasLoaded = true
      this.indexToShow = 0
      // this.toggleButton()
    },
    goBack () {
      this.selectedTab = 'QDes'
      this.tabWasLoaded = false
    },
    showQu () {
      // Show only Q and A according to relevant qID
      // Set value of var indexToShow
    },
    toggleButton () {
      // Depending on reserved keyword in question/answer, show/hide Next/Finish buttons
      this.showNextBtn = false
      this.showFinishBtn = true
    },
    goNext () {
      // Navigation logic. depending on Default ID or answer ID
      // input: next QID from current Q/Ans or Default ID || Output: navigation + add to answer object
      // check next question Id from current answer ID --> related to current index via search in tracking index
      var formIndex = this.currFIndex
      var questionIndex = this.currQIndex
      var answerIndex = this.currAIndex
      // Check for next quID in answerChoices or default Id of Question. If answer choice is empty, go up and see whether parent ID is empty
      var nextQId = this.forms[formIndex].questions[questionIndex].answerChoices[answerIndex].nextQuId
      if (nextQId === '') {
        this.$q.notify('No next QID')
        // Check if default ID is filled with integer
        var keywordQu = this.forms[formIndex].questions[questionIndex].nextDefaultId.toUpperCase()
        if (keywordQu === 'ENDFORM' || keywordQu === '') {
          this.$q.notify('End form when answer is empty : ' + keywordQu)
          // Call Finish function
        } else {
          nextQId = this.forms[formIndex].questions[questionIndex].nextDefaultId
          this.$q.notify('Nxt Q ID 1 Def ID: ' + nextQId)
          var isnum = /^[0-9]+$/.test(nextQId)
          if (isnum === true) {
            this.getQuIdIndex(parseInt(nextQId, 10))
          } else {
            // Call Finish method
          }
        }
      } else {
      // Check in Question Tracking Array for the corresponding index
        this.$q.notify('Nxt Q ID2: ' + nextQId)
        this.getQuIdIndex(nextQId)
      }
      // Show next Question
      this.indexToShow = this.currQIndex
      // this.toggleButton()
      // this.addAnswers()
    },
    finishForm () {
      // Button is showed only if keyword in Default ID
      // Output: Saves to answer object. Closes form.
    },
    // Tracking Array Methods
    addTrackingId (fIndex) {
      var qObj = this.forms[fIndex].questions
      // to get last index, need length of object as we always add to last index
      var lastIndexQObj = Object.keys(qObj).length - 1
      // this.$q.notify('Final index in  questions: ' + lastIndexQObj)
      this.trackingID.push({
        quesID: this.genQuIdCounter,
        quesIndex: lastIndexQObj
      })
    },
    getQuIdIndex (nextQId) {
      var found = this.trackingID.find(track => track.quesID === nextQId)
      if (typeof found !== 'undefined') {
        const valIndexOfId = found.quesIndex
        this.$q.notify('Value of Index: ' + valIndexOfId)
        this.currQIndex = valIndexOfId
      } else if (typeof found === 'undefined') {
        // This means the index does not exist. See if this should trigger finish function
      }
    },
    // Misc methods
    refreshPage () {
      location.reload(true)
    },
    timeStamp1 (date, locale) {
      const event = (date === undefined) ? new Date() : new Date(date)
      return event.toLocaleDateString(locale) + ' ' + event.toLocaleTimeString(locale)
    },
    checkDefId () {
      // Depending on reserved keyword in question/answer, show/hide Next/Finish buttons
      // input: Default Id
      var formIndex = this.currFIndex
      var answerIndex = this.currAIndex
      var questionIndex = this.currQIndex
      var keywordQu = this.forms[formIndex].questions[questionIndex].nextDefaultId.toUpperCase()
      var keywordAn = this.forms[formIndex].questions[questionIndex].answerChoices[answerIndex].nextQuId
      if (keywordQu === 'ENDFORM' || keywordQu === '' || keywordAn === '') {
        this.$q.notify('KWord : ' + keywordQu)
      }
    }
  }
}
</script>
