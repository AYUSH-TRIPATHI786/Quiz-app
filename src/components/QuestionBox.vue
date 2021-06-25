<template>
    <div class="question-box-container">
        <b-jumbotron >
            <template #lead>
                {{currentQuestion.question}}
            </template>

            <hr class="my-4">
            <b-list-group>
                <b-list-group-item v-for =  '(answer,index) in shuffledAnswers' :key='index'
                    @click="selectanswer(index)"
                    :class="answerClass(index)"
                >
                    {{answer}}
                </b-list-group-item>
            </b-list-group>

            <b-button 
                variant="primary"
                @click ="submitAnswer()"
                :disabled="selectedIndex===null || isAnswered"
                >
                Submit
            </b-button>
            <b-button @click='next' variant="success" href="#">
                Next
            </b-button>
        </b-jumbotron>
    </div>
</template>

<script>
export default {
    props: {
        currentQuestion : Object,
        next:Function,
        increment:Function
    },
    watch:{
        currentQuestion:{
            immediate:true,
            handler(){
                this.selectedIndex=null
                this.shuffleAnswers()
                this.isAnswered=false
            }
        }

    },
    data(){
        return {
        selectedIndex:null,
        shuffledAnswers:[],
        correctIndex:0,
        isAnswered:false

        }
    },
    methods:{
        selectanswer(index){
            if(this.isAnswered) return
            this.selectedIndex=index
            
        },
        shuffleAnswers(){
            let answers = [...this.currentQuestion.incorrect_answers,this.currentQuestion.correct_answer]
            this.correctIndex=answers.length-1
            this.shuffle(answers)
            this.shuffledAnswers=answers
            this.correctIndex=this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
            return answers
        },
        shuffle(array){
            let index=-1
            let size = array.length
            let j=0,temp=''  ;
            while(++index<size){
                j = Math.floor(Math.random() * (size))
                temp = array[j]
                array[j]= array[index]
                array[index]=temp
                
                
            }
        },
        submitAnswer(){
            if(this.selectedIndex===this.correctIndex){
                this.increment(true)
            }
            else{
                this.increment(false)
            }
            this.isAnswered=true
        },
        answerClass(index){
            let answerClass = ''

            if(!this.isAnswered && this.selectedIndex==index){
                answerClass = 'selected'
            }else if(this.isAnswered && this.correctIndex===index) {
                answerClass = 'correct'
            }else if(this.isAnswered && 
                this.correectIndex!==index && this.selectedIndex === index) {
                answerClass = 'incorrect'
            }
            return answerClass
        }
    },
    computed:{
        answers(){
            let answers = [...this.currentQuestion.incorrect_answers]
            answers.push(this.currentQuestion.correct_answer)
            return answers
        }
        
    },

}

</script>

<!-- scoped means it will not be global and affect only this component  -->
<style scoped>
    .jumbotron{
        background: #ddd;
    }
    .list-group{
        margin-bottom: 15px;
        margin: 25px;
    }
    .list-group-item:hover{
        background: #eee;
        cursor: pointer;
    }
    .btn{
        margin-right: 5px;
        margin-bottom: 5px;
        margin-left: 5px;
        margin-top: 5px;
    }
    .selected{
        background-color: lightblue;
    }
    .correct{
        background-color: lightgreen;
    }
    .incorrect{
        background-color: red;
    }
    

</style>