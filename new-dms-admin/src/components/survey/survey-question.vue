<template>
    <div class="survey-question">
        <div class="underbar-question">
            <span class="survey-title">{{surveyTitle}}</span>
            <span class="survey-question-form-add-btn-group">
              <span>객관식</span><img src="../../assets/icon/ic_plus.png" class="survey-question-form-add-btn" @click="multifulChoiceBtn()"/>
              <span>주관식</span><img src="../../assets/icon/ic_plus.png" class="survey-question-form-add-btn" @click="descriptiveBtn()"/>
            </span>
        </div>
            
        <div class="survey-question-form">
          <component :is="surveyQuestionForm" v-for="(surveyQuestion, index) in questions" :key="index" :isObjective="surveyQuestion.is_objective" :index="index" @submitQuestion="surveyQuestionGet" @multifulTitleGet="multifulTitleEmit" @multifulTextGet="multifulTextEmit" class="survey-question-add-component"></component>
        </div>
        <div class="survey-question-edit-btn-group">
          <button @click="surveyQuestionSubmit">등록</button>
          <button @click="surveyEditCancel">취소</button>
        </div>
        <component :is="'modal'" v-if="isModal" @modalClose="modalToggle"></component>
    </div>
</template>

<script>
import surveyQuestionForm from './child/survey-question-form'
import modal from './child/modal'
import eventBus from './eventBus'

export default {
  name: 'Survey',
  components: { surveyQuestionForm, modal },
  props: ['surveyTitle', 'surveyId'],
  data: function () {
    return {
      questions: [],
      // { Authorization: '',
      //   id: '',
      //   title: '',
      //   is_objective: null,
      //   choice_paper: []}

      // survey_id: '',
      // Authorization: '',
      // surveyTitle: '',
      surveyQuestionForm: surveyQuestionForm,
      isModal: false
    }
  },
  methods: {
    multifulChoiceBtn: function () {
      this.questions.push({
        title: '',
        is_objective: true,
        choice_paper: []
      })
    },
    descriptiveBtn: function () {
      this.questions.push({
        title: '',
        is_objective: false,
        choice_paper: []
      })
    },
    surveyQuestionSubmit: function () {
      this.$axios.post('/admin/survey/question', JSON.stringify({
        survey_id: this.surveyId,
        questions: this.questions
      }),
        {
          headers: {
            'Authorization': 'JWT ' + this.$getCookie('JWT'),
            'Content-Type': 'application/json'
          }
        })
      .then((response) => {
        console.log('Good!!!')
        alert('질문 등록에 성공하셨습니다 !!!')
        eventBus.$emit('change-view', 'surveyList')
      })
      .catch((ex) => {
        console.log('ERROR!!!! : ', ex)
        this.modalToggle()
      })
    },
    surveyQuestionGet: function (title, index) {
      this.questions[index].title = title
    },
    multifulTitleEmit: function (title, index) {
      this.questions[index].title = title
    },
    multifulTextEmit: function (text, index) {
      this.questions[index].choice_paper.push(text)
    },
    surveyEditCancel: function () {
      eventBus.$emit('change-view', 'surveyMain')
    },
    modalToggle: function () {
      this.isModal = !this.isModal
    }
  }
}
</script>

<style scoped>
@import url(http://fonts.googleapis.com/earlyaccess/jejugothic.css);
* {
  font-family: 'Jeju Gothic', serif;
}
.underbar-question {
  width : 100%;
  border-bottom : 2px solid grey;
  display: flex;
  justify-content: space-between;
}
.survey-question-form-add-btn {
  cursor: pointer;
  width: 1.7vw;
  height: 1.7vw;
}
.survey-question-form-add-btn-group {
  display: table;
}
.survey-question-form-add-btn-group > span {
  display: table-cell;
  vertical-align: middle;
  padding-left: 3.5vw;
  padding-right: 1.3vw;
}
.survey-title {
  margin-left : 1vw;
  font-size : 30px;
  margin-bottom : .7vh;
}
.survey-question-title {
  margin-left : 1vw;
  font-size : 20px;
}
.survey-question-text {
  width: 91.6vw;
  margin-left : 1vw;
}
.survey-question-form {
  height: 50vh;
  overflow-y: auto; 
}
.survey-question-edit-btn-group {
  margin-top:4.3vh;
  text-align : center;
}
.survey-question-edit-btn-group > button {
  background-color: #675094;
  border : 1px solid #c8c8c8;
  border-radius: 1px;
  padding : 2.3vh 2.6vw 2.3vh 2.6vw;
  color:white;
  border-radius: 20px;
  cursor: pointer;
}
.survey-question-add-component {
  margin-top: 2vh;
  padding-bottom: 2vh;
  border-bottom:1px solid grey;
}
</style>
