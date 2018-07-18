<template>
  <q-page>
    <q-tabs color="secondary" glossy align="justify">
        <q-tab slot="title" default name="QDes" icon="subject" label="QDesign"/>
        <q-tab slot="title" name="QDesPos" icon="subject" label="QDesignPost"/>
      <!-- QDes Tab -->
        <q-tab-pane name="QDes">
          <q-card class="bg-cyan-2 q-ma-xl">
            <q-card-main>
            <div v-for="(form, fIndex) in forms" :key="form.id">
              <q-field class="q-mb-sm" label="Form Title: " helper="Please enter the title of the question.">
                <q-input v-model="form.name" type="text" clearable />
              </q-field>
              <q-card-separator class="q-mb-md q-mt-xl"/>
           <div v-for="(question, qIndex) in form.questions" :key="question.id">
            <q-btn class="q-mb-md" round size="sm" color="amber" icon="add" @click="addRowQuestions(fIndex)" />
            <q-btn class="vertical-top" v-show="qIndex !==0" round size="sm" color="blue" icon="remove" @click="remRowQs(fIndex)" />
            <q-field class="q-mb-sm" label="Question Title: " >
              <q-input v-model="question.text" />
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
          </q-card>
        </q-tab-pane>
          <!-- QDesPos Tab -->
        <q-tab-pane name="QDesPos">
        </q-tab-pane>
      </q-tabs>
  </q-page>
</template>

<script>

export default {
  data () {
    return {
      forms: [
        {
          name: '',
          questions: [
            {
              text: '',
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
        text: '',
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
      this.forms[fIndex].questions.answerChoices.splice(fIndex, 1)
    },
    remRowAns (fIndex, qIndex) {
      this.forms[fIndex].questions[qIndex].answerChoices.splice(qIndex, 1)
    }
  }
}
</script>
