<template>
  <q-page class="flex flex-center quiz-page">
    <q-card class="quiz-container shadow-10">
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
            outline
            rounded
            color="pink-4"
            text-color="brown-8"
            class="option-btn"
          />
        </div>

        <q-input
          v-else
          v-model="textAnswer"
          filled
          rounded
          label="Digite sua resposta..."
          color="pink-4"
          bg-color="pink-1"
          class="q-mt-md"
          :disable="feedbackMessage !== ''"
          @keyup.enter="submitAnswer"
        />

        <q-btn
          label="Responder"
          color="deep-orange-8"
          size="lg"
          rounded
          class="q-mt-lg full-width"
          @click="submitAnswer"
          :disable="!selectedOption && !textAnswer && (currentQuestion.type !== 'text')"
        />

        <q-banner
          v-if="feedbackMessage"
          :class="feedbackCorrect ? 'bg-green-2 text-green-9' : 'bg-red-2 text-red-9'"
          rounded
          class="q-mt-md"
        >
          {{ feedbackMessage }}
        </q-banner>
      </q-card-section>

      <q-card-section v-else class="result-section">
        <div class="text-h5 text-primary dancing-script">Resultados do Quiz!</div>
        <div class="text-h6 q-mt-md text-grey-8">
          Você acertou {{ correctAnswersCount }} de {{ questions.length }} perguntas!
        </div>

        <div v-if="correctAnswersCount >= 7" class="vale-livro-card q-mt-lg shadow-6">
          <q-icon name="book" size="64px" color="teal-6" class="q-mb-md" />
          <div class="text-h5 text-primary dancing-script">Parabéns, Meu Amor!</div>
          <div class="text-subtitle1 text-grey-8">
            Você acertou {{ correctAnswersCount }} perguntas e ganhou um presente especial!
          </div>
          <div class="text-h4 text-weight-bold text-dark q-mt-md amazon-code">
            {{ amazonGiftCode }}
          </div>
          <q-btn
            label="Copiar Código"
            color="orange-8"
            rounded
            class="q-mt-md"
            @click="copyCode"
          />
          <div class="text-caption text-grey-6 q-mt-sm">
            Válido para compras na Amazon.com.br. Valor: R$ 50,00.
            <br>
            Cuidado: O código não aparecerá novamente!
          </div>
        </div>
        <div v-else class="q-mt-lg text-h6 text-grey-7">
          Não foi dessa vez, mas o seu amor já é o meu maior prêmio! ❤️
        </div>
        <q-btn
          label="Jogar Novamente"
          color="deep-orange-6"
          rounded
          class="q-mt-lg"
          @click="resetQuiz"
        />
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import { useQuasar } from 'quasar';

const $q = useQuasar();

interface Question {
  question: string;
  options?: string[]; 
  answer: string;
  type?: 'text' | 'options';
}

const amazonGiftCode = ref<string>("ABCD-EFGHIJ-KLMNOP"); 

const questions: Ref<Question[]> = ref([
  {
    question: "Qual foi o filme que assistimos no nosso primeiro encontro?",
    options: ["Vingadores: Ultimato", "La La Land", "Como Se Fosse a Primeira Vez", "O Poderoso Chefão"],
    answer: "Como Se Fosse a Primeira Vez"
  },
  {
    question: "Em que local tivemos nosso primeiro beijo?",
    options: ["Na porta da sua casa", "No cinema", "Na festa do João", "No Parque da Cidade"],
    answer: "Na porta da sua casa"
  },
  {
    question: "Qual é a música que mais nos lembra o início do nosso relacionamento?",
    options: ["Photograph - Ed Sheeran", "Perfect - Ed Sheeran", "Thinking Out Loud - Ed Sheeran", "All of Me - John Legend"],
    answer: "Photograph - Ed Sheeran"
  },
  {
    question: "Qual foi a primeira viagem que fizemos juntos?",
    options: ["Porto de Galinhas", "Serra Talhada", "Recife", "Fernando de Noronha"],
    answer: "Porto de Galinhas" 
  },
  {
    question: "Qual é o meu apelido carinhoso para você?",
    type: "text",
    answer: "Meu Amorzinho" 
  },
  {
    question: "Qual a comida que mais me agrada quando você prepara?",
    options: ["Pizza caseira", "Lasanha", "Um bom churrasco", "Salmão grelhado"],
    answer: "Lasanha" 
  },
  {
    question: "Em qual ocasião você me deu o presente mais inusitado (e divertido)?",
    options: ["No nosso aniversário de namoro", "No meu aniversário", "No Natal", "Sem data especial, só por carinho"],
    answer: "Sem data especial, só por carinho" 
  },
  {
    question: "Qual é o seu sonho de viagem que ainda não realizamos juntos?",
    options: ["Paris, França", "Tóquio, Japão", "Fernando de Noronha, Brasil", "Cruzeiro pelo Caribe"],
    answer: "Fernando de Noronha, Brasil" 
  },
  {
    question: "Qual a minha mania mais engraçada, segundo você?",
    type: "text",
    answer: "Roncar baixinho" 
  },
  {
    question: "Qual foi a maior surpresa que você já me preparou?",
    options: ["A festa de aniversário surpresa", "Um jantar romântico inesperado", "Uma viagem de fim de semana de última hora", "Um bilhete de amor escondido"],
    answer: "A festa de aniversário surpresa" 
  },
  {
    question: "Qual é o meu hobby favorito que você sempre me incentiva a praticar?",
    type: "text",
    answer: "Cozinhar" 
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
    question: "Qual é a sua flor favorita que eu sempre tento te dar?",
    options: ["Rosas vermelhas", "Girassóis", "Lírios", "Orquídeas"],
    answer: "Rosas vermelhas" 
  },
  {
    question: "Qual é a atividade mais simples que fazemos juntos e que você mais ama?",
    options: ["Assistir a um filme no sofá", "Cozinhar juntos no fim de semana", "Caminhar de mãos dadas no parque", "Fazer um piquenique"],
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

.quiz-page {
  background: linear-gradient(to bottom right, #FFC0CB, #FF69B4);
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  overflow: hidden;
}

.quiz-page::before {
    content: '❤️';
    position: absolute;
    top: -20px;
    left: -20px;
    font-size: 150px;
    color: rgba(255, 0, 0, 0.1);
    transform: rotate(-25deg);
    z-index: 0;
}

.quiz-container {
  max-width: 700px;
  width: 90%;
  padding: 30px;
  border-radius: 20px;
  background-color: rgba(255, 255, 255, 0.95);
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
  text-align: center;
  z-index: 1; 
}

.dancing-script {
  font-family: 'Dancing Script', cursive;
  font-weight: 700;
}

.text-primary {
  color: #8B0000 !important; 
}

.text-pink-4 {
  color: #FF69B4 !important;
}

.q-card__section {
  padding: 20px;
}

.options-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 15px;
  margin-top: 20px;
}

.option-btn {
  font-size: 1.1em;
  padding: 15px 25px;
  transition: all 0.3s ease;
  font-family: 'Playfair Display', serif;
  &:hover {
    background-color: #FF69B4 !important;
    color: white !important;
    transform: translateY(-3px);
    box-shadow: 0 5px 15px rgba(0,0,0,0.1);
  }
}

.selected-option {
  background-color: #FF69B4 !important;
  color: white !important;
  border-color: #8B0000 !important;
}

.q-input {
  .q-field__control {
    border-radius: 8px;
  }
}

.result-section {
  background-color: #FFF0F5;
  border-radius: 15px;
  padding: 30px;
  box-shadow: inset 0 0 15px rgba(0,0,0,0.1);
}

.vale-livro-card {
  background-color: #FAEBD7;
  border: 3px dashed #FF69B4;
  padding: 25px;
  margin-top: 30px;
  border-radius: 15px;
  box-shadow: 0 5px 20px rgba(0,0,0,0.1);
  animation: pulse 1.5s infinite alternate;
}

.amazon-code {
  font-family: 'Courier New', monospace;
  font-size: 1.8em;
  font-weight: bold;
  letter-spacing: 2px;
  user-select: all;
  background-color: #ffe0b3;
  padding: 10px 20px;
  border-radius: 8px;
  display: inline-block;
}

@keyframes pulse {
    0% { transform: scale(1); }
    100% { transform: scale(1.03); }
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