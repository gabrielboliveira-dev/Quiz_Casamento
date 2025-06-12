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

        <div v-if="correctAnswersCount >= 7" class="vale-livro-card neomorphic-card-prize q-mt-lg">
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
// (O script setup permanece o mesmo, sem alterações significativas)
// Importações e lógica como antes
import { ref, computed } from 'vue';
import { useQuasar } from 'quasar';

const $q = useQuasar();

// Interface para tipar as perguntas
interface Question {
  question: string;
  options?: string[]; // '?' indica que é opcional
  answer: string;
  type?: 'text' | 'options'; // 'text' para input de texto, 'options' para múltipla escolha (padrão)
}

const amazonGiftCode = ref<string>("ABCD-EFGHIJ-KLMNOP"); // <-- **ADICIONE SEU CÓDIGO REAL AQUI**

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
    answer: "Porto de Galinhas" // Ajuste para a sua realidade
  },
  {
    question: "Qual é o meu apelido carinhoso para você?",
    type: "text",
    answer: "Meu Amorzinho" // Adapte para o apelido real!
  },
  {
    question: "Qual a comida que mais me agrada quando você prepara?",
    options: ["Pizza caseira", "Lasanha", "Um bom churrasco", "Salmão grelhado"],
    answer: "Lasanha" // Ajuste para a sua realidade
  },
  {
    question: "Em qual ocasião você me deu o presente mais inusitado (e divertido)?",
    options: ["No nosso aniversário de namoro", "No meu aniversário", "No Natal", "Sem data especial, só por carinho"],
    answer: "Sem data especial, só por carinho" // Adapte!
  },
  {
    question: "Qual é o seu sonho de viagem que ainda não realizamos juntos?",
    options: ["Paris, França", "Tóquio, Japão", "Fernando de Noronha, Brasil", "Cruzeiro pelo Caribe"],
    answer: "Fernando de Noronha, Brasil" // Ajuste para a sua realidade
  },
  {
    question: "Qual a minha mania mais engraçada, segundo você?",
    type: "text",
    answer: "Roncar baixinho" // Adapte para a mania real!
  },
  {
    question: "Qual foi a maior surpresa que você já me preparou?",
    options: ["A festa de aniversário surpresa", "Um jantar romântico inesperado", "Uma viagem de fim de semana de última hora", "Um bilhete de amor escondido"],
    answer: "A festa de aniversário surpresa" // Adapte!
  },
  {
    question: "Qual é o meu hobby favorito que você sempre me incentiva a praticar?",
    type: "text",
    answer: "Cozinhar" // Adapte!
  },
  {
    question: "Se fôssemos um super-herói/heroína juntos, qual seria nosso superpoder?",
    options: ["Teletransporte para viajar instantaneamente", "Voar pelo mundo de mãos dadas", "Controlar o tempo para reviver momentos especiais", "Super força para carregar todas as compras"],
    answer: "Teletransporte para viajar instantaneamente" // Adapte!
  },
  {
    question: "Qual foi o momento em que você sentiu que 'era para sempre' comigo?",
    type: "text",
    answer: "Quando passamos a primeira noite juntos conversando sobre tudo" // Adapte!
  },
  {
    question: "Qual é a sua flor favorita que eu sempre tento te dar?",
    options: ["Rosas vermelhas", "Girassóis", "Lírios", "Orquídeas"],
    answer: "Rosas vermelhas" // Adapte!
  },
  {
    question: "Qual é a atividade mais simples que fazemos juntos e que você mais ama?",
    options: ["Assistir a um filme no sofá", "Cozinhar juntos no fim de semana", "Caminhar de mãos dadas no parque", "Fazer um piquenique"],
    answer: "Assistir a um filme no sofá" // Adapte!
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

  // Desabilita botões/inputs enquanto mostra o feedback
  setTimeout(() => {
    feedbackMessage.value = ''; // Limpa o feedback
    selectedOption.value = null; // Reseta a opção selecionada
    textAnswer.value = ''; // Limpa a resposta de texto

    currentQuestionIndex.value++;
    if (currentQuestionIndex.value >= questions.value.length) {
      quizFinished.value = true;
    }
  }, 2000); // 2 segundos para dar tempo de ler o feedback
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

// --- Variáveis de Neomorfismo ---
$light-shadow: -4px -4px 8px rgba(255, 255, 255, 0.7);
$dark-shadow: 4px 4px 8px rgba(0, 0, 0, 0.15);
$inset-light-shadow: inset 4px 4px 8px rgba(255, 255, 255, 0.7);
$inset-dark-shadow: inset -4px -4px 8px rgba(0, 0, 0, 0.15);

// Cores base para neomorfismo (tons suaves de rosa e branco)
$neumo-bg: #f8f0f4; // Rosa muito claro
$neumo-lighter: #ffffff; // Branco
$neumo-darker: #e8d8e0; // Rosa um pouco mais escuro

// Paleta de cores do Quiz (ajustadas para harmonizar com o neomorfismo)
$quiz-primary-color: #c2185b; // Rosa mais vibrante para títulos
$quiz-secondary-color: #e91e63; // Rosa secundário
$quiz-accent-color: #fce4ec; // Rosa claro (para gradiente)
$quiz-text-dark: #212121; // Cinza escuro
$quiz-text-light: #757575; // Cinza claro
$quiz-success: #43a047;
$quiz-error: #e53935;
$quiz-brown: #795548; // Marrom suave

.quiz-page {
  background: linear-gradient(to bottom right, $quiz-accent-color, $neumo-bg); // Suave gradiente rosa
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  overflow: hidden;
  font-family: 'Montserrat', sans-serif; // Fonte sem serifa para o corpo
}

.quiz-page::before {
  content: '❤️';
  position: absolute;
  top: -20px;
  left: -20px;
  font-size: 150px;
  color: rgba(255, 105, 180, 0.05); // Rosa mais forte e mais transparente
  transform: rotate(-25deg);
  z-index: 0;
}

.quiz-container {
  max-width: 700px;
  width: 90%;
  padding: 30px;
  border-radius: 20px;
  background-color: $neumo-bg;
  box-shadow: $light-shadow, $dark-shadow; // Neomorfismo base
  text-align: center;
  z-index: 1;
  transition: all 0.3s ease; // Transições suaves
}

.neomorphic-card {
  background-color: $neumo-bg;
  border-radius: 20px;
  box-shadow: $light-shadow, $dark-shadow;
  transition: all 0.3s ease;
}

.dancing-script {
  font-family: 'Dancing Script', cursive;
  font-weight: 700;
  color: $quiz-primary-color; // Cor rosa para títulos
}

.text-primary {
  color: $quiz-primary-color !important;
}

.text-pink-4 {
  color: $quiz-secondary-color !important;
}

.text-grey-8 {
  color: $quiz-text-light !important;
}

.options-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 20px; // Aumentar o gap para mais "respiro"
  margin-top: 25px;
}

.neomorphic-btn {
  background-color: $neumo-bg;
  border-radius: 15px; // Um pouco menos arredondado que o card
  box-shadow: $light-shadow, $dark-shadow;
  transition: all 0.3s ease;
  color: $quiz-text-dark; // Cor do texto base
  font-weight: 500;
  padding: 15px 25px;

  &:hover {
    box-shadow: $dark-shadow, $light-shadow; // Inverte as sombras para "pressionar"
    transform: translateY(1px); // Pequeno movimento para indicar clique
    color: $quiz-primary-color; // Ação no hover
  }

  &.selected-option {
    background-color: $neumo-lighter; // Um tom ligeiramente diferente para o selecionado
    box-shadow: $inset-dark-shadow, $inset-light-shadow; // Sombra interna para "pressionado"
    color: $quiz-secondary-color; // Cor destacada para selecionado
    font-weight: 600;
  }
}

.neomorphic-btn-primary {
  background-color: $quiz-secondary-color; // Cor de destaque para o botão principal
  color: white;
  box-shadow: $dark-shadow, $light-shadow; // Sombra para parecer elevado
  &:hover {
    background-color: darken($quiz-secondary-color, 5%);
    box-shadow: $inset-dark-shadow, $inset-light-shadow; // Sombra interna para "pressionar"
  }
  &:active {
    box-shadow: $inset-dark-shadow, $inset-light-shadow;
    transform: translateY(1px);
  }
}

.neomorphic-btn-copy {
  background-color: #FF8C00; // Laranja para copiar
  color: white;
  box-shadow: $dark-shadow, $light-shadow;
  &:hover {
    background-color: darken(#FF8C00, 5%);
    box-shadow: $inset-dark-shadow, $inset-light-shadow;
  }
  &:active {
    box-shadow: $inset-dark-shadow, $inset-light-shadow;
    transform: translateY(1px);
  }
}

.neomorphic-input {
  background-color: $neumo-bg !important;
  border-radius: 15px;
  box-shadow: $inset-dark-shadow, $inset-light-shadow; // Sombra interna para o input
  font-family: 'Montserrat', sans-serif;
  padding: 10px 15px; // Ajuste o padding para neomorfismo

  .q-field__control {
    background: transparent !important; // Garante que o fundo seja o do container
    box-shadow: none !important;
    border-radius: 15px;
    color: $quiz-text-dark;
  }
  .q-field__label {
    color: $quiz-text-light;
  }
}

.neomorphic-banner-success {
  background-color: rgba($quiz-success, 0.2) !important;
  color: $quiz-success !important;
  box-shadow: $inset-light-shadow, $inset-dark-shadow;
  border-radius: 15px;
}

.neomorphic-banner-error {
  background-color: rgba($quiz-error, 0.2) !important;
  color: $quiz-error !important;
  box-shadow: $inset-light-shadow, $inset-dark-shadow;
  border-radius: 15px;
}

.result-section {
  background-color: $neumo-bg;
  border-radius: 20px;
  box-shadow: $light-shadow, $dark-shadow; // Continua o estilo do card
  padding: 30px;
}

.vale-livro-card {
  background-color: $neumo-lighter; // Um tom um pouco mais claro para o card de prêmio
  border-radius: 20px;
  box-shadow: $light-shadow, $dark-shadow;
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
  background-color: $neumo-darker; // Um tom mais escuro para o background do código
  box-shadow: $inset-dark-shadow, $inset-light-shadow; // Sombra interna
  padding: 10px 20px;
  border-radius: 8px;
  display: inline-block;
  color: $quiz-text-dark;
}

@keyframes pulse {
    0% { transform: scale(1); box-shadow: $light-shadow, $dark-shadow; }
    100% { transform: scale(1.02); box-shadow: $light-shadow, $dark-shadow,
             0 0 15px rgba($quiz-secondary-color, 0.3); } // Adiciona um brilho sutil
}

/* Responsividade (manter, mas pode ajustar margens/paddings) */
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
// --- TERMINA AQUI O CÓDIGO QUE VOCÊ FORNECEU ---
</style>