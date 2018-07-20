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
              <q-field class="q-mb-sm" label="Form Title: " helper="Please enter the title of the form.">
                <q-input v-model="form.fname" type="text" clearable />
              </q-field>
              <q-card-separator class="q-mb-md q-mt-xl"/>
           <div v-for="(question, qIndex) in form.questions" :key="question.id">
            <q-btn class="q-mb-md" round size="sm" color="amber" icon="add" @click="addRowQuestions(fIndex)" />
            <q-btn class="vertical-top" v-show="qIndex !==0" round size="sm" color="blue" icon="remove" @click="remRowQs(fIndex)" />
            <q-field class="q-mb-sm" label="Question Title: " >
              <q-input v-model="question.qtext" />
            </q-field>
            <q-card-separator class="q-mb-md q-mt-xl"/>
              <div v-for="(answerChoice, aIndex) in question.answerChoices" :key="answerChoice.id">
                <q-btn class="q-mb-md" round size="sm" color="green" icon="add" @click="addAnswers(fIndex, qIndex)" />
                <q-btn class="vertical-top" v-show="aIndex !==0" round size="sm" color="negative" icon="remove" @click="remRowAns(fIndex, qIndex)" />
                <q-field class="q-mb-sm" label="Answer ID: ">
                  <q-input v-model="answerChoice.answerId" type="number" clearable />
                </q-field>
            <q-card-separator class="q-mb-md q-mt-xl"/>
              </div>
           </div>
           </div>
            </q-card-main>
          <q-btn class="q-ml-md q-mb-md" color="red" icon-right="navigate_next" @click="generateForm">Generate the form</q-btn>
          </q-card>
        </q-tab-pane>
          <!-- QDesPos Tab -->
        <q-tab-pane name="QDesPos">
            <q-card class="bg-blue-2 q-ma-xl">
            <q-card-title>Resulting form
              <span slot="subtitle">View the designed form.</span>
            </q-card-title>
            <q-card-separator class="q-mb-md q-mt-xl"/>
            <q-card-main>
            <div v-for="(form) in forms" :key="form.id">
              <q-field class="q-mb-sm" label="Form Title: " >
                <q-input v-model="form.fname" />
              </q-field>
            </div>
            </q-card-main>
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
      forms: [
        {
          fname: '',
          questions: [
            {
              qtext: '',
              answerChoices: [
                {
                  answerId: ''
                }
              ]
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
        answerChoices: [
          {
            answerId: ''
          }
        ]
      })
    },
    addAnswers (fIndex, qIndex) {
      this.forms[fIndex].questions[qIndex].answerChoices.push({
        answerId: ''
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
      this.posTest()
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
    }
  }
}
</script>
