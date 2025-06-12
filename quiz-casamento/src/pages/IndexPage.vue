<template>
  <q-page class="flex flex-center quiz-page">
    <q-card class="quiz-container neomorphic-card">
      <q-card-section>
        <div class="text-h4 text-weight-bold text-primary q-mb-md dancing-script">
          Quiz do Amor: Nossa História
        </div>
      </q-card-section>

      <q-card-section v-if="!quizFinished">
        <div class="text-h6 text-grey-8 q-mb-lg">{{ currentQuestion.question }}</div>

        <div v-if="!currentQuestion.type || currentQuestion.type === 'options'" class="options-grid">
          <q-btn
            v-for="(option, index) in currentQuestion.options"
            :key="index"
            :label="option"
            @click="selectOption(option)"
            :class="{ 'selected-option': selectedOption === option }"
            :disable="feedbackMessage !== ''"
            flat
            rounded
            class="option-btn neomorphic-btn"
          />
        </div>

        <q-input
          v-else
          v-model="textAnswer"
          rounded
          label="Digite sua resposta..."
          color="pink-4"
          bg-color="pink-1"
          class="q-mt-md neomorphic-input"
          :disable="feedbackMessage !== ''"
          @keyup.enter="submitAnswer"
          borderless
        />

        <q-btn
          label="Responder"
          size="lg"
          rounded
          class="q-mt-lg full-width neomorphic-btn neomorphic-btn-primary"
          @click="submitAnswer"
          :disable="!selectedOption && !textAnswer && (currentQuestion.type !== 'text')"
          flat
        />

        <q-banner
          v-if="feedbackMessage"
          :class="feedbackCorrect ? 'neomorphic-banner-success' : 'neomorphic-banner-error'"
          rounded
          class="q-mt-md"
        >
          {{ feedbackMessage }}
        </q-banner>
      </q-card-section>

      <q-card-section v-else class="result-section neomorphic-card">
        <div class="text-h5 text-primary dancing-script">Resultados do Quiz!</div>
        <div class="text-h6 q-mt-md text-grey-8">
          Você acertou {{ correctAnswersCount }} de {{ questions.length }} perguntas!
        </div>

        <div v-if="correctAnswersCount >= 10" class="vale-livro-card neomorphic-card-prize q-mt-lg">
          <q-icon name="book" size="64px" color="teal-6" class="q-mb-md" />
          <div class="text-h5 text-primary dancing-script">Parabéns, Meu Amor!</div>
          <div class="text-subtitle1 text-grey-8">
            Você acertou {{ correctAnswersCount }} perguntas e ganhou um presente especial!
          </div>
          <div class="text-h4 text-weight-bold text-dark q-mt-md neomorphic-code-display">
            {{ amazonGiftCode }}
          </div>
          <q-btn
            label="Copiar Código"
            rounded
            class="q-mt-md neomorphic-btn neomorphic-btn-copy"
            @click="copyCode"
            flat
          />
          <div class="text-caption text-grey-6 q-mt-sm">
            Válido para compras na Amazon.com.br. Valor: R$ 100,00.
            <br>
            Cuidado: O código não aparecerá novamente!
          </div>
        </div>
        <div v-else class="q-mt-lg text-h6 text-grey-7">
          Não foi dessa vez, mas o seu amor já é o meu maior prêmio! ❤️
        </div>
        <q-btn
          label="Jogar Novamente"
          rounded
          class="q-mt-lg neomorphic-btn neomorphic-btn-primary"
          @click="resetQuiz"
          flat
        />
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script setup lang="ts">

import { ref, computed } from 'vue';
import type { Ref } from 'vue';
import { useQuasar } from 'quasar';

const $q = useQuasar();


interface Question {
  question: string;
  options?: string[]; 
  answer: string;
  type?: 'text' | 'options'; 
}

const amazonGiftCode = ref<string>("IREI-PASSAR-PIX");

const questions: Ref<Question[]> = ref([
  {
    question: "Qual foi a primeira saga de filmes que assistimos ?",
    options: ["Marvel", "Crepúsculo", "Harry Potter", "Disney"],
    answer: "Crepúsculo"
  },
  {
    question: "Em que local tivemos nosso primeiro beijo?",
    options: ["Na porta da sua casa", "No cinema", "Na festa do João", "Na minha casa"],
    answer: "Na minha casa"
  },
  {
    question: "Qual é a música que mais nos lembra o início do nosso relacionamento?",
    options: ["Photograph - Ed Sheeran", "All of Me - John Legend", "Thinking Out Loud - Ed Sheeran", "Nós dois - Pedro Valença"],
    answer: "Nós dois - Pedro Valença"
  },
  {
    question: "Qual será nossa primeira viagem internacionalque faremos juntos?",
    options: ["Roma", "Paris", "Lisboa", "Londres"],
    answer: "Lisboa"
  },
  {
    question: "Qual é o meu apelido carinhoso para você?",
    type: "text",
    answer: "Amor"
  },
  {
    question: "Qual a primeira comida que mais me agradou quando você preparou?",
    options: ["Pizza caseira", "Lasanha", "Tapioca", "Misto"],
    answer: "Tapioca" 
  },
  {
    question: "Em qual lugar mais iamos comer no namoro/noivado?",
    options: ["Klebyo Pizzaria", "Serra China", "Tempero Sertanejo", "Caquinha"],
    answer: "Klebyo Pizzaria"
  },
  {
    question: "Qual o seu sonho de viagem de lua de mel? OBS: Iremos realizar!",
    options: ["São Caetano", "Santa Cruz da Baixa Verde", "Praia do Pipa", "Cruzeiro pelo Caribe"],
    answer: "Praia do Pipa"
  },
  {
    question: "Qual a sua mania mais engraçada, segundo eu?",
    type: "text",
    answer: "Roncar baixinho"
  },
  {
    question: "Onde foi nosso pedio de noivado?",
    options: ["Klebyo Pizzaria", "Serra China", "Tempero Sertanejo", "Caquinha"],
    answer: "Tempero Sertanejo" 
  },
  {
    question: "Qual é o meu hobby favorito que você sempre me incentiva a praticar?",
    type: "text",
    answer: "Execitar"
  },
  {
    question: "Se fôssemos um super-herói/heroína juntos, qual seria nosso superpoder?",
    options: ["Teletransporte para viajar instantaneamente", "Voar pelo mundo de mãos dadas", "Controlar o tempo para reviver momentos especiais", "Super força para carregar todas as compras"],
    answer: "Teletransporte para viajar instantaneamente" 
  },
  {
    question: "Qual foi o momento em que você sentiu que 'era para sempre' comigo?",
    type: "text",
    answer: "Quando passamos a primeira noite juntos conversando sobre tudo" 
  },
  {
    question: "Qual é a flor que gostaria de receber?",
    options: ["Rosas vermelhas", "Girassóis", "Lírios", "Copo-de-leite"],
    answer: "Copo-de-leite" 
  },
  {
    question: "Qual é a atividade mais simples que fazemos juntos e que você mais ama?",
    options: ["Assistir a um filme no sofá", "Cozinhar juntos no fim de semana", "Caminhar de mãos", "Cozinhar juntos"],
    answer: "Assistir a um filme no sofá" 
  }
]);

const currentQuestionIndex = ref<number>(0);
const correctAnswersCount = ref<number>(0);
const selectedOption = ref<string | null>(null);
const textAnswer = ref<string>('');
const feedbackMessage = ref<string>('');
const feedbackCorrect = ref<boolean>(false);
const quizFinished = ref<boolean>(false);

const currentQuestion = computed<Question>(() => {
  return questions.value[currentQuestionIndex.value];
});

function selectOption(option: string): void {
  selectedOption.value = option;
}

function submitAnswer(): void {
  const q: Question = currentQuestion.value;
  let userAnswer: string = '';

  if (q.type === 'text') {
    userAnswer = textAnswer.value.trim();
  } else {
    if (selectedOption.value === null) {
      $q.notify({
        message: 'Por favor, selecione uma opção!',
        color: 'negative',
        position: 'top',
        timeout: 1000
      });
      return;
    }
    userAnswer = selectedOption.value;
  }

  const normalizedUserAnswer = userAnswer.toLowerCase().replace(/\s+/g, ' ').trim();
  const normalizedCorrectAnswer = q.answer.toLowerCase().replace(/\s+/g, ' ').trim();

  if (normalizedUserAnswer === normalizedCorrectAnswer) {
    correctAnswersCount.value++;
    feedbackMessage.value = "Correto! ❤️";
    feedbackCorrect.value = true;
  } else {
    feedbackMessage.value = `Ops! A resposta correta era: "${q.answer}" 😢`;
    feedbackCorrect.value = false;
  }

  
  setTimeout(() => {
    feedbackMessage.value = ''; 
    selectedOption.value = null; 
    textAnswer.value = ''; 

    currentQuestionIndex.value++;
    if (currentQuestionIndex.value >= questions.value.length) {
      quizFinished.value = true;
    }
  }, 2000);
}

function copyCode(): void {
  navigator.clipboard.writeText(amazonGiftCode.value).then(() => {
    $q.notify({
      message: 'Código copiado para a área de transferência! 🎉 Agora é só escolher seu livro na Amazon!',
      color: 'positive',
      position: 'top',
      timeout: 3000
    });
  }).catch((err: unknown) => {
    console.error('Falha ao copiar: ', err);
    $q.notify({
      message: 'Não foi possível copiar o código automaticamente. Por favor, copie manualmente: ' + amazonGiftCode.value,
      color: 'negative',
      position: 'top',
      timeout: 5000
    });
  });
}

function resetQuiz(): void {
  currentQuestionIndex.value = 0;
  correctAnswersCount.value = 0;
  selectedOption.value = null;
  textAnswer.value = '';
  feedbackMessage.value = '';
  feedbackCorrect.value = false;
  quizFinished.value = false;
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Dancing+Script:wght@400;700&family=Playfair+Display:ital,wght@0,400..900;1,400..900&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@400;500;600&display=swap');

$glass-bg: rgba(255, 255, 255, 0.15);
$glass-border: rgba(255, 255, 255, 0.5);
$quiz-primary-color: #c2185b;
$quiz-secondary-color: #e91e63;
$quiz-accent-color: #fce4ec;
$quiz-text-dark: #212121;
$quiz-text-light: #757575;
$quiz-success: #43a047;
$quiz-error: #e53935;

.quiz-page {
  background: linear-gradient(to bottom right, $quiz-accent-color, white);
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  overflow: hidden;
  font-family: 'Montserrat', sans-serif;
}

.quiz-page::before {
  content: '❤️';
  position: absolute;
  top: -20px;
  left: -20px;
  font-size: 200px;
  color: rgba(255, 105, 180, 0.05);
  transform: rotate(-25deg);
  z-index: 0;
}

.quiz-container {
  max-width: 700px;
  width: 90%;
  padding: 30px;
  border-radius: 20px;
  background: $glass-bg;
  backdrop-filter: blur(40px);
  -webkit-backdrop-filter: blur(40px);
  border: 1px solid $glass-border;
  text-align: center;
  z-index: 1;
  transition: all 0.3s ease;
}

.neomorphic-card {
  background: $glass-bg;
  border: 1px solid $glass-border;
  backdrop-filter: blur(30px);
  border-radius: 20px;
  transition: all 0.3s ease;
}

.dancing-script {
  font-family: 'Dancing Script', cursive;
  font-weight: 700;
  color: $quiz-primary-color;
}

.text-primary { color: $quiz-primary-color !important; }
.text-pink-4 { color: $quiz-secondary-color !important; }
.text-grey-8 { color: $quiz-text-light !important; }

.options-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 20px;
  margin-top: 25px;
}

.neomorphic-btn {
  background: $glass-bg;
  border: 1px solid $glass-border;
  backdrop-filter: blur(15px);
  border-radius: 20px;
  transition: all 0.3s ease;
  color: $quiz-text-dark;
  font-weight: 500;
  padding: 15px 25px;

  &:hover {
    transform: scale(1.02);
    color: $quiz-primary-color;
    background: rgba(255, 255, 255, 0.25);
  }

  &.selected-option {
    background: rgba(255, 255, 255, 0.3);
    border: 1px solid rgba(255, 255, 255, 0.4);
    color: $quiz-secondary-color;
    font-weight: 600;
  }
}

.neomorphic-btn-primary {
  background-color: $quiz-secondary-color;
  color: white;
  border: none;
  //&:hover {
    //background-color: darken($quiz-secondary-color, 5%);
  //}
  &:active {
    transform: scale(0.98);
  }
}

.neomorphic-btn-copy {
  background-color: #FF8C00;
  color: white;
  border: none;
  &:hover {
    background-color: darken(#FF8C00, 5%);
  }
  &:active {
    transform: scale(0.98);
  }
}

.neomorphic-input {
  background: rgba(255, 255, 255, 0.15) !important;
  border: 1px solid rgba(255, 255, 255, 0.3);
  border-radius: 20px;
  backdrop-filter: blur(8px);
  font-family: 'Montserrat', sans-serif;
  padding: 10px 15px;

  .q-field__control {
    background: transparent !important;
    border-radius: 20px;
    color: $quiz-text-dark;
  }

  .q-field__label {
    color: $quiz-text-light;
  }
}

.neomorphic-banner-success,
.neomorphic-banner-error {
  border-radius: 20px;
  backdrop-filter: blur(6px);
  border: 1px solid rgba(255, 255, 255, 0.3);
  padding: 15px;
}

.neomorphic-banner-success {
  background-color: rgba($quiz-success, 0.2) !important;
  color: $quiz-success !important;
}

.neomorphic-banner-error {
  background-color: rgba($quiz-error, 0.2) !important;
  color: $quiz-error !important;
}

.result-section {
  background: $glass-bg;
  border: 1px solid $glass-border;
  backdrop-filter: blur(10px);
  border-radius: 20px;
  padding: 30px;
}

.vale-livro-card {
  background: rgba(255, 255, 255, 0.3);
  border: 1px solid rgba(255, 255, 255, 0.4);
  border-radius: 20px;
  backdrop-filter: blur(8px);
  padding: 25px;
  margin-top: 30px;
  animation: pulse 1.5s infinite alternate;
}

.neomorphic-code-display {
  font-family: 'Courier New', monospace;
  font-size: 1.8em;
  font-weight: bold;
  letter-spacing: 2px;
  user-select: all;
  background: rgba(255, 255, 255, 0.2);
  border: 1px solid rgba(255, 255, 255, 0.3);
  backdrop-filter: blur(6px);
  padding: 10px 20px;
  border-radius: 8px;
  color: $quiz-text-dark;
}

@keyframes pulse {
  0% {
    transform: scale(1);
    box-shadow: 0 0 0 rgba(0, 0, 0, 0);
  }
  100% {
    transform: scale(1.02);
    box-shadow: 0 0 15px rgba($quiz-secondary-color, 0.3);
  }
}

@media (max-width: 600px) {
  .quiz-container {
    padding: 20px;
  }
  .text-h4 {
    font-size: 2em !important;
  }
  .text-h6 {
    font-size: 1.2em !important;
  }
  .option-btn {
    font-size: 1em;
    padding: 12px 20px;
  }
  .amazon-code {
    font-size: 1.2em;
    padding: 8px 15px;
  }
}
</style>
