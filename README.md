# Quiz do Amor: Nossa História ❤️

Este é um projeto especial de Dia dos Namorados, um **quiz interativo** desenvolvido com **Quasar Framework (Vue 3, TypeScript, Vite)** para celebrar a nossa história! Ele foi feito com carinho para a minha esposa, para que ela possa testar o quanto se lembra dos momentos mais importantes e divertidos do nosso relacionamento.

---

## Funcionalidades

- **Perguntas Personalizadas:** Questões sobre o nosso relacionamento, desde o primeiro encontro até hoje.
- **Interface Bonita e Responsiva:** Desenvolvida com Quasar Framework, garantindo uma ótima experiência em qualquer dispositivo (desktop, tablet, celular).
- **Feedback Instantâneo:** Mostra se a resposta está correta ou incorreta após cada pergunta.
- **Surpresa Final:** Se a pontuação for alta o suficiente, um **vale-presente da Amazon** é revelado como prêmio!
- **Reiniciar Quiz:** Opção para jogar novamente e reviver as memórias.

---

## Como Executar Localmente (Para Desenvolvedores)

Se você quiser explorar o código ou fazer suas próprias modificações, siga estes passos:

1.  **Pré-requisitos:**

    - [Node.js](https://nodejs.org/en/download/) (versão LTS recomendada)
    - npm (vem com Node.js) ou Yarn

2.  **Instale a CLI do Quasar globalmente (se ainda não tiver):**

    ```bash
    npm install -g @quasar/cli
    # ou
    yarn global add @quasar/cli
    ```

3.  **Clone o Repositório:**

    ```bash
    git clone [https://github.com/gabrielboliveira-dev/quiz-casamento.git](https://github.com/gabrielboliveira-dev/quiz-casamento.git)
    cd quiz-casamento
    ```

4.  **Instale as Dependências:**

    ```bash
    npm install
    # ou
    yarn
    ```

5.  **Inicie o Servidor de Desenvolvimento:**
    ```bash
    quasar dev
    ```
    O aplicativo será aberto automaticamente no seu navegador (geralmente em `http://localhost:8080`).

---

## Configuração para Deploy (GitHub Pages)

Este projeto está configurado para ser facilmente hospedado no GitHub Pages.

1.  **Configure o `publicPath`:**
    No arquivo `quasar.config.js`, certifique-se de que o `build.publicPath` esteja configurado com o nome do seu repositório (ex: `/quiz-casamento/`).

    ```typescript
    // quasar.config.js
    build: {
      publicPath: '/quiz-casamento/',
      // ...
    },
    ```

2.  **Gere a Build de Produção:**

    ```bash
    quasar build
    ```

    Isso criará a pasta `dist/spa` com os arquivos otimizados.

3.  **Deploy para GitHub Pages:**
    Use `git subtree` para enviar apenas a pasta `dist/spa` para a branch `gh-pages`:

    ```bash
    # Se ainda não configurou o remote:
    # git remote add origin [https://github.com/gabrielboliveira-dev/quiz-casamento.git](https://github.com/gabrielboliveira-dev/quiz-casamento.git)

    git add dist -f
    git commit -m "Build for GitHub Pages"
    git subtree push --prefix dist/spa origin gh-pages
    ```

4.  **Ative o GitHub Pages:**
    Vá para as **`Settings`** do seu repositório no GitHub, clique em **`Pages`**, selecione a branch **`gh-pages`** e a pasta **`/ (root)`**, e salve. O site estará disponível em `https://gabrielboliveira-dev.github.io/quiz-casamento/` em alguns minutos.

---

## Personalização

- **Perguntas:** Edite o array `questions` em `src/pages/IndexPage.vue` com as suas próprias perguntas e respostas.
- **Código do Vale-Presente:** **Não se esqueça de inserir o código real do vale-presente da Amazon** na variável `amazonGiftCode` em `src/pages/IndexPage.vue` antes do deploy!
- **Estilos:** Altere as cores, fontes e outros estilos no bloco `<style lang="scss">` de `src/pages/IndexPage.vue`.
- **Ícone do Livro:** Substitua `src/assets/book_icon.png` por um ícone de sua preferência.

---

## ❤️ Feito com Amor

Este projeto é um gesto de carinho e uma forma de usar minhas habilidades de programação para criar algo único e memorável para minha esposa Thaís Teodosio.

---

**Autor:** [gabrielboliveira-dev]

---
