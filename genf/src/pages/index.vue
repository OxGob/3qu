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
              <q-btn class="q-mt-sm" label="Add More Questions" color="blue" icon="add" @click="addRowQuestions(fIndex)" />
              <q-card-separator class="q-mb-md q-mt-lg"/>
      <!-- QDes - Questions -->
        <q-card class="bg-teal-1 q-mt-md q-mb-md">
           <div v-for="(question, qIndex) in form.questions" :key="question.id">
            <!-- <q-btn class="q-mb-md" round size="sm" color="amber" icon="add" @click="addRowQuestions(fIndex)" /> -->
            <q-card-separator class="q-mb-md q-mt-lg"/>
            <q-btn class="q-mb-md q-ml-md q-mr-sm" v-show="qIndex !==0" color="red-3" icon="remove" label="Remove this question" @click="remRowQs(fIndex, qIndex)" />
            <q-field class="q-mb-sm q-mt-sm q-ml-sm q-mr-sm" label="Question ID: " helper="This Question ID is automatically generated. This IS NOT displayed to the user and is for INTERNAL use only.">
              <q-input v-model="question.qId" type="number" align="center" readonly />
            </q-field>
            <q-field class="q-mb-sm q-mt-md q-ml-sm q-mr-sm" label="Question Type: " helper="Please select a question type. This IS NOT displayed to the user and is for INTERNAL use only.">
              <q-select v-model="question.qType" :options="form.quSelOptions" />
            </q-field>
            <q-field class="q-mb-sm q-ml-sm q-mr-sm" label="Question: " helper="Please enter a question. This IS displayed to the user.">
              <q-input v-model="question.qtext" type="textarea" rows="6" align="center" clearable />
            </q-field>
            <q-field class="q-mb-sm q-ml-sm q-mr-sm" label="Help: " helper="Please enter a description for any helper label. This IS displayed to the user.">
              <q-input v-model="question.qHelp" type="text" align="center" clearable />
            </q-field>
             <q-field class="q-mb-sm q-ml-sm q-mr-sm" label="Default/Next Question ID: " helper="Please enter the next Question ID (a number) to proceed. To terminate the form, use the reserved keyword ENDFORM or enter -1. This IS NOT displayed to the user and is for INTERNAL use only.">
              <q-input v-model="question.nextDefaultId" type="text" align="center" clearable />
            </q-field>
            <div  v-show="question.qType !== 'freetext'">
            <q-card-separator class="q-mb-md q-mt-lg"/>
            <q-btn class="q-ml-sm q-mt-sm" color="green" label="Add More Answers" icon="add" @click="addAnswerChoices(fIndex, qIndex)" />
            </div>
      <!-- QDes - Answers -->
          <q-card class="bg-green-2 q-ml-md q-mt-lg q-mb-md q-mr-md">
            <div  v-show="question.qType !== 'freetext'">
              <div v-for="(answerChoice, aIndex) in question.answerChoices" :key="answerChoice.id">
                <!-- <q-btn class="q-mb-md" round size="sm" color="green" icon="add" @click="addAnswerChoices(fIndex, qIndex, aIndex)" /> -->
                <q-btn class="q-mb-md q-ml-md" v-show="aIndex !==0" color="green-3" icon="remove" label="Remove this answer" @click="remRowAns(fIndex, qIndex, aIndex)" />
                <q-field class="q-mb-sm q-ml-sm q-mt-lg q-mr-sm" label="Answer Label: " helper="This Answer label is editable. This IS NOT displayed to the user and is for INTERNAL use only.." >
                  <q-input v-model="answerChoice.answerId" type="number" align="center" clearable/>
                </q-field>
                <q-field class="q-mb-sm q-ml-sm q-mr-sm" label="Answer Text: " helper="Please enter the answer text. e.g. Yes or No. This IS displayed to the user.">
                  <q-input v-model="answerChoice.text" type="text" align="center" clearable />
                </q-field>
                 <!-- <q-field class="q-mb-sm q-ml-sm q-mr-sm" label="Answer Value: " helper="Please enter the answer value. This IS NOT displayed to the user and is for INTERNAL use only." >
                  <q-input v-model="answerChoice.answerValue" type="number" align="center" clearable />
                </q-field> -->
                <q-field class="q-mb-sm q-ml-sm q-mr-sm" label="Next Question ID: " helper="Please enter the next Question ID (a number) to proceed. To terminate the form, leave the field blank. This IS NOT displayed to the user and is for INTERNAL use only." >
                  <q-input v-model="answerChoice.nextQuId" type="number" align="center" onkeypress="return event.charCode >= 48 && event.charCode <= 57" clearable />
                </q-field>
                <q-card-separator class="q-mb-md q-mt-lg"/>
              </div>
            </div>
          </q-card>
           </div>
          </q-card>
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
              <q-btn class="float-right q-mr-md" color="black" label="load Test JSON" @click="testLoadJSON"/>
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
                <q-field class="q-ml-md q-mt-md q-mb-md" label="Question: " :helper="question.qHelp" >
                  <q-input v-model="question.qtext" type="textarea" rows="6" align="center" readonly/>
                </q-field>
                <q-card-separator class="q-mb-md q-mt-md"/>
          <!-- QDesPos - Answers -->
                  <q-card class="bg-green-2 q-ml-md q-mt-lg q-mb-md q-mr-md">
                  <div  v-show="question.qType === 'single'">
                    <q-field class="q-ml-md q-mt-md q-mb-md" label="Select one please: " />
                    <div v-for="(answerChoice) in question.answerChoices" :key="answerChoice.id">
                        <q-radio class="q-ml-md q-mb-md" v-model="form.ansRadioVal" :val="answerChoice.answerId" :label=" answerChoice.text" @input="radioSelected"/>
                    </div>
                  </div>
                  <div  v-show="question.qType === 'freetext'">
                      <q-field class="q-ml-md q-mt-md q-mb-md" label="Answer: " helper="Please write an answer." >
                        <q-input class="q-mb-md" v-model="form.tempAnsHolder" align="center" type="textarea" rows="6" clearable/>
                      </q-field>
                  </div>
                  </q-card>
                  <div  v-show="showNextBtn">
                  <q-btn class="q-ml-md q-mb-md q-mt-md" icon-right="navigate_next" color="blue-7" label="Next Question" @click="nextTapped(question.qType)"/>
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
      qTrackingID: [
        {
          quesID: 0,
          quesIndex: 0
        }
      ],
      indexToShow: 0,
      currFIndex: 0,
      currQIndex: 0,
      currAIndex: 0,
      showNextBtn: true,
      showFinishBtn: false,
      forms: [
        {
          ansRadioVal: '',
          counterGenQuID: 0,
          counterAnswers: 0,
          tempAnsHolder: '',
          fname: '',
          fDescription: '',
          questions: [
            {
              qtext: '',
              qHelp: '',
              qId: 0, // integer
              nextDefaultId: '', // if empty or undefined or keyword, then complete form after this question
              qType: 'single',
              answerChoices: [
                {
                  answerId: 0,
                  text: '',
                  // answerValue: '', // e.g. y
                  nextQuId: '' // integer. If empty or undefined or keyword, then complete form after this question
                }
              ],
              ansTrackingID: [
                {
                  ansID: 0,
                  ansIndex: 0
                }
              ],
              counterAnsChID: 0
            }
          ],
          answers: [
            {
              questionId: '',
              answerText: '',
              answerId: '',
              timeStamp: ''
            }
          ],
          quSelOptions: [
            {
              label: 'Freetext',
              value: 'freetext'
            },
            // {
            //   label: 'Multiple choice',
            //   value: 'multi'
            // },
            {
              label: 'Single choice',
              value: 'single'
            }
          ]
        }
      ]
    }
  },
  methods: {
    // Add Remove Functions Section
    // This function allows questions to be added.
    addRowQuestions (fIndex) {
      this.forms[fIndex].counterGenQuID++
      this.forms[fIndex].questions.push({
        qtext: '',
        qHelp: '',
        qId: this.forms[fIndex].counterGenQuID,
        nextDefaultId: '',
        qType: 'single',
        answerChoices: [
          {
            answerId: 0,
            text: '',
            // answerValue: '', // e.g. y
            nextQuId: '' // integer. If empty or undefined or keyword, then complete form after this question
          }
        ],
        ansTrackingID: [
          {
            ansID: 0,
            ansIndex: 0
          }
        ],
        counterAnsChID: 0
      })
      this.addTrackQuId(fIndex)
    },
    // This function allows answer choices per question to be added.
    addAnswerChoices (fIndex, qIndex) {
      this.forms[fIndex].questions[qIndex].counterAnsChID++
      this.forms[fIndex].questions[qIndex].answerChoices.push({
        answerId: this.forms[fIndex].questions[qIndex].counterAnsChID,
        text: '',
        // answerValue: '', // e.g. y
        nextQuId: ''
      })
      this.addTrackAnsId(fIndex, qIndex)
    },
    // This function allows the selected question to be removed.
    remRowQs (fIndex, qIndex) {
      this.forms[fIndex].questions.splice(qIndex, 1)
      // remove the selected index from the question tracking Array
      this.qTrackingID.splice(qIndex, 1)
      // In the tracking array, update the question index for those removed AFTER SPLICE
      var lengthOfTrackerAfterSplice = Object.keys(this.qTrackingID).length
      if (qIndex < lengthOfTrackerAfterSplice) {
      // If qIndex is NOT LAST, then from qIndex position till last, subtract 1 from values of quesIndex
        var i
        for (i = qIndex; i < lengthOfTrackerAfterSplice; i++) {
          var fn = this.qTrackingID[i].quesIndex - 1
          this.qTrackingID[i].quesIndex = fn
        }
      }
    },
    // This function allows the selected answer to be removed.
    remRowAns (fIndex, qIndex, aIndex) {
      var pos = this.forms[fIndex].questions[qIndex]
      pos.answerChoices.splice(aIndex, 1)
      // remove the selected index from the answer tracking Array
      pos.ansTrackingID.splice(aIndex, 1)
      // In the tracking array, update the question index for those removed AFTER SPLICE
      var lengthOfTrackerAfterSplice = Object.keys(pos.ansTrackingID).length
      if (aIndex < lengthOfTrackerAfterSplice) {
      // If aIndex is NOT LAST, then from aIndex position till last, subtract 1 from values of ansIndex
        var i
        for (i = aIndex; i < lengthOfTrackerAfterSplice; i++) {
          var fn = pos.ansTrackingID[i].ansIndex - 1
          pos.ansTrackingID[i].ansIndex = fn
        }
      }
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
    toggleFinishBtn () {
      // Called to hide/show Next/Finish buttons
      this.showNextBtn = false
      this.showFinishBtn = true
    },
    finishForm () {
      this.$q.notify('Arrived at finish stage!')
    },
    // This function is called when the user clicks on the next button.
    nextTapped (quType) {
      // 1. If qType = single choice, perform search for answer choices. If qType =  freetext. Save Answer.
      if (quType === 'single') {
        this.searchAnsChoicesRadio(quType)
      } else if (quType === 'freetext') {
        // save answer
        var ansFreeId = ''
        var ansFreeTxt = this.forms[this.currFIndex].tempAnsHolder
        this.saveAnswers(ansFreeId, ansFreeTxt)
        this.searchNextQuestion(quType, null)
      }
      // Show the next question
      this.indexToShow = this.currQIndex
    },
    // SEARCH METHODS
    // This function searches for the next question. Called by nextTapped (), searchAnsChoicesRadio ()
    searchNextQuestion (quType, questId) {
      // Algo for searching for next Question.
      // If SINGLE. look at question / Answer Next QU ID
      // 1. Answer has a next Qu Id ==> use this for next (ANS Next QU ID) —> ( CAN CODE)
      // 2. Answer has no next Qu Id ==> go to Parent Qu ID  —> ( CAN CODE)
      // 2.1. Parent Qu ID has a next Qu Id ==> use this for next (PARENT Next QU ID)
      var formIndex = this.currFIndex
      var questionIndex = this.currQIndex
      var nextQId = this.forms[formIndex].questions[questionIndex].nextDefaultId
      var nextQIdAns = questId // From radio choice by user
      switch (quType) {
        case 'freetext':
          // If FREETEXT, look for next Qu Id in order: Question --> Array of questions
          // NB IMPORTANT: Qu Id Ans ID is ONLY integers for now. No freetext yet. Amend comment when editable is implemented --> 1
          if (nextQId !== '') {
          // Check next question Id (END or PROCEED).
            this.checkNextQId(nextQId)
          } else {
          // NB IMPORTANT: NO NEXT QU ID in QUESTION ==> Get next question from list of array. Remove comment when done. --> 3
          }
          break
        case 'single':
          // If SINGLE. look for next Qu Id in order: Answer  --> Question --> Array of questions
          // 1. Answer has a next Qu Id in Ans Ch ==> use this for next (ANS Next QU ID) —> ( CAN CODE)
          if (nextQIdAns !== '') {
          // Check next question Id (END or PROCEED)
            this.checkNextQId(nextQIdAns)
          } else if (nextQIdAns === '') {
          // 2. Answer has no next Qu Id ==> go to Parent Qu ID
            if (nextQId !== '') {
              // Check next question Id (END or PROCEED)
              this.checkNextQId(nextQId)
            } else {
            // NB IMPORTANT: NO NEXT QU ID in QUESTION ==> Get next question from list of array. Remove when done. --> 6
            }
          }
          break
      }
    },
    // This function searches the answerchoices for the one matching the radio button value. Called for Single question type.
    // Returns the answer choice. Saved as answer. Next QuId is used.
    searchAnsChoicesRadio (quType) {
      var val = this.forms[this.currFIndex].ansRadioVal
      // FOR TESTING USE Object BELOW in Answer Choices
      // [{"answerId":0,"text":"sdfsfsd","answerValue":1111,"nextQuId":""},{"answerId":2,"text":"cvbcvb","answerValue":2323,"nextQuId":"34"}]
      var ansChSe = this.forms[this.currFIndex].questions[this.currQIndex].answerChoices
      var foundAnsCh = ansChSe.find(ans => ans.answerId === val)
      var foundText = foundAnsCh.text
      var foundAnsId = foundAnsCh.answerId
      console.log('Found Ans Ch is via: ', foundAnsCh)
      if (typeof foundAnsCh !== 'undefined') {
        // Save Answer choice in answers
        this.saveAnswers(foundAnsId, foundText)
        this.searchNextQuestion(quType, foundAnsCh.nextQuId)
      } else if (typeof foundAnsCh === 'undefined') {
        // This means the index does not exist. Error Condition. Send an alert to user.
        this.$q.notify('search Found Ansch is undefined. We cannot find the search index ')
      }
    },
    // TO MAKE GO NEXT REdundant
    goNext () {
      // 1. If qType = single choice, perform search for answer choices.
      // ==> this gives answer
      // => this gives next Q Id
      // If qType =  freetext. Save Answer. Then, use next QId in default ID for searchNextQuestion(). do step (3) If not present, then look at scenario 2.2 End or get next question
      // 2. Save answer
      // this.saveAnswers()
      // 2. Find next QID. Perform search after saving answer
      // 3. Toggle Button
      // this.toggleButton()
      // 4. Show next Question
      this.indexToShow = this.currQIndex
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
        if (keywordQu === 'ENDFORM' || keywordQu === '-1') {
          this.$q.notify('End form when answer is empty : ' + keywordQu)
          // Call Finish function
        } else {
          nextQId = this.forms[formIndex].questions[questionIndex].nextDefaultId
          // [CV]: if nextQId is empty, this function returns the next available question (following the order of the array)
          // [CV]: Something on the line of: nextQId = this.forms[formIndex].questions[questionIndex+1]
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
    },
    // This function gets the index of the next Question ID to display
    getQuIdIndex (nextQId) {
      var foundQIn = this.qTrackingID.find(track => track.quesID === nextQId)
      if (typeof foundQIn !== 'undefined') {
        const valIndexOfId = foundQIn.quesIndex
        // this.$q.notify('Value of Index: ' + valIndexOfId)
        this.currQIndex = valIndexOfId
      } else if (typeof foundQIn === 'undefined') {
        // NB: This means the index does not exist. See if this should trigger finish function. Send an alert to user.
      }
    },
    // TRACKING ARRAYS METHODS
    addTrackQuId (fIndex) {
      var qObj = this.forms[fIndex].questions
      // to get last index, need length of object as we always add to last index
      var lastIndexQObj = Object.keys(qObj).length - 1
      // this.$q.notify('Final index in  questions: ' + lastIndexQObj)
      this.qTrackingID.push({
        quesID: this.forms[fIndex].counterGenQuID,
        quesIndex: lastIndexQObj
      })
    },
    // This function is called when adding an answer Choice
    addTrackAnsId (fIndex, qIndex) {
      var aObj = this.forms[fIndex].questions[qIndex].answerChoices
      // to get last index, need length of object as we always add to last index
      var lastIndexAObj = Object.keys(aObj).length - 1
      // this.$q.notify('Final index in  questions: ' + lastIndexQObj)
      this.forms[fIndex].questions[qIndex].ansTrackingID.push({
        ansID: this.forms[fIndex].questions[qIndex].counterAnsChID,
        ansIndex: lastIndexAObj
      })
    },
    // ANSWERS METHODS
    // This function allows answers input by the user to be added to the answers arrays.
    saveAnswers (ansId, ansText) {
      var formIndex = this.currFIndex
      var questionIndex = this.currQIndex
      var savAnsInd = this.forms[formIndex].counterAnswers
      // Once index 0 is filled, will need to create a new answers object for any new one and fill that in. Increment Answers counter.
      if (savAnsInd === 0) {
        this.forms[formIndex].answers[savAnsInd].questionId = this.forms[formIndex].questions[questionIndex].qId
        this.forms[formIndex].answers[savAnsInd].answerText = ansText
        this.forms[formIndex].answers[savAnsInd].answerId = ansId
        this.forms[formIndex].answers[savAnsInd].timeStamp = this.timeStamp1(new Date(), 'en-gb')
        this.forms[formIndex].counterAnswers++
      } else if (savAnsInd > 0) {
        this.createAnswersObj(formIndex, questionIndex, ansId, ansText)
        this.forms[formIndex].counterAnswers++
      }
    },
    // This function creates new populated answers object.
    createAnswersObj (formIndex, questionIndex, ansId, ansText) {
      this.forms[formIndex].answers.push({
        questionId: this.forms[formIndex].questions[questionIndex].qId,
        answerText: ansText,
        answerId: ansId,
        timeStamp: this.timeStamp1(new Date(), 'en-gb')
      })
    },
    // Misc methods
    refreshPage () {
      location.reload(true)
    },
    timeStamp1 (date, locale) {
      const event = (date === undefined) ? new Date() : new Date(date)
      return event.toLocaleDateString(locale) + ' ' + event.toLocaleTimeString(locale)
    },
    // Radio selected only for TESTING. TO BE MADE REDUNDANT
    radioSelected () {
      this.$q.notify('The value from radio is: ' + this.forms[this.currFIndex].ansRadioVal)
    },
    // This function checks the next Qu Id to either end or proceed. Called by searchNextQuestion().
    checkNextQId (qIdCheck) {
      // if there is a keyword, call finish. Otherwise proceed to get next question id
      if (qIdCheck === 'ENDFORM' || qIdCheck === '-1') {
        this.toggleFinishBtn()
      } else if (qIdCheck !== '') {
        // NB: To add a check here for a valid next question Id. REMOVE WHEN DONE ---> 1
        // If the check returns a valid one call this.getQuIdIndex for next Qu Id
        this.getQuIdIndex(parseInt(qIdCheck, 10))
      }
    },
    // TESTING METHODS
    // This function is called from the genereated form and loads a JSON into forms for testing.
    testLoadJSON () {
      // Loads a JSON
      this.$q.notify('load tst json')
    }
  }
}
</script>
