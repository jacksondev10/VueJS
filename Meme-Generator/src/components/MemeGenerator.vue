<template>
  <div class="row m-0 p-2">
    <div class="col-md-6 text-center mb-4">
      <div id="main-content" class="d-block mx-auto position-relative">
        <p class="fixed-top text" :style="textColor">
          {{ text.top }} 
          <!-- Recebe os dados do input colocados pelo usuario , e fixados no topo
          recebendo a casse "fixed-top" -->
          
        </p>
<!-- Recebendo a imagem através de props -->
        <img :src="image.src"
             :alt="image.title"
             :title="image.title"
             id="main-image">

        <p class="fixed-bottom text" :style="textColor">
          {{ text.bottom }} <!--Recebendo dados do input colocados pelo usuario, 
          fixados na parte superior , recebendo a classe "fixed-bottom" -->
        </p>
      </div>
    </div>

    <div class="col-md-6">
      <h1 v-if="showTitle" class="text-center"> <!-- A diretiva v-if é usada para renderizar condicionalmente um bloco.
       O bloco só será renderizado se a expressão da diretiva retornar um valor verdadeiro. -->
        <small>{{ title }}</small>
      </h1>

      <div class="form-group">
        <input
          type="text"
          placeholder="Top text"
          class="form-control"
          v-model="text.top" 
        >
        <!-- Diretiva V-Model responsavel por criar a ligação de dados na entrada do formulário -->
      </div>

      <div class="form-group">
        <input
          type="text"
          placeholder="Bottom text"
          class="form-control"
          v-model="text.bottom"
        >
         <!-- Diretiva V-Model responsavel por criar a ligação de dados na entrada do formulário -->
      </div>

      <div class="form-group">
        <div class="custom-control custom-checkbox" @click="toggleTextUppercase()">
          <input
            type="checkbox"
            class="custom-control-input"
            :checked="text.uppercase"
          >
          <!-- Evento @click dispara o evento que dispara a função de alteração da classe para Uppercase  ao ser selecionada -->

          <label class="custom-control-label">UPPERCASE</label>
        </div>
      </div>

      <div class="form-group">
        <label>TEXT COLOR</label>
        <span v-for="(color, index) in colors" :key="index"> <!-- Diretiva para renderizar lista com base de itens no array, as cores estão dentro de "Color" in data -->

          <span class="ml-2 border border-primary"
                :style="colorStyle(color)"
                @click="updateTextColor(color)"></span>
        </span>
        <!-- ":style" permitindo a alteração de vários objetos de estilo ao mesmo elemento diretiva muito importante chamada V-Bind -->
      </div>

      <div class="form-group">
        <input
          class="form-control-file"
          accept="image/*"
          type="file"
          @change="previewImage($event)"
        >
        <!-- Evento para visualizar a imagem recebida pelo input type "file"-->
      </div>

      <button class="btn btn-outline-primary" @click="resetInputs()">
        <!--Limpar os campos , retorna o value para " "   . -->
        Reset
      </button>
      <button class="btn btn-outline-danger ml-2" @click="download()">
        Download
        <!-- Descarrega a imagem em seu dispositivo -->
      </button>
    </div>
  </div>
</template>

<script>

import html2canvas from 'html2canvas';
import FileSaver from 'file-saver';

export default {
  name: 'MemeGenerator', // Nome do componente

  data() {
    return {
      title: 'Meme Generator',  // Titulo instânciado
      image: {
        src: 'images/i-have-no-idea.png', // Ao invés de enviar a imagem direta pelo html, foi criada a propriedade props //
        title: 'Dog',
      },
      text: {  // Conjunto de informações declaradas pelo usuario //
        top: '',
        bottom: '',
        color: 'white',
        uppercase: true,
      },
      colors: ['red', 'black', 'white'], // Array de cores do qual v-for acima teve interatividade para selecionar as cores //
      showTitle: false, // Se verdadeiro aplica a cor declarada //
    };
  },

  computed: { // Propriedade computada para interagir com condições de estilo a serem exibidos no html, " Se selecionado aplica o estilo uppercase //
    textColor() {
      const uppercase = this.text.uppercase ? 'uppercase' : 'lowercase';
      return { color: this.text.color, textTransform: uppercase };
    },
  },

  methods: { // Propriedade criada criar o seletor das cores //
    colorStyle(color) { 
      return {
        width: '16px',
        height: '16px',
        borderRadius: '50%',
        display: 'inline-block',
        background: color,
        cursor: 'pointer'
      };
    },

    resetInputs() { // Limpa os Inputs //
      this.text.top = '';
      this.text.bottom = '';
      this.text.color = 'white';
    },

    updateImage(image) { // Altera imagem // 
      this.image.src = image.src;
      this.image.title = image.title;
    },

    updateTextColor(color) { // Altera Cor //
      this.text.color = color;
    },

    previewImage(event) { //Visualizar imagem selecionada//
      let input = event.target;

      if (input.files && input.files[0]) {
        let render = new FileReader();

        render.onload = (e) => {
          this.image.src = e.target.result;
        };

        render.readAsDataURL(input.files[0]);
      }
    },

    printImageToCanvas() { //Permitir que o usuario salve a imagem //
      return html2canvas(document.querySelector('#main-content'));
    },

    toggleTextUppercase() {  // Converte o estilo da fonte //
      this.text.uppercase = !this.text.uppercase;
    },

    async download() { //  Download // 
      const canvas = await this.printImageToCanvas();
      window.open(canvas.toBlob(function(blob) {
        FileSaver.saveAs(blob, 'meme.png')
      }), '_self');
    },
  },
}
</script>

<style scoped>

#main-content {
  word-wrap: break-word;
}

#main-image {
  width: 100%;
  height: auto;
  max-height: 800px;
}

.text {
  color: #fff;
  font-family: 'Passion One';
  font-size: 4.5vw;
  line-height: 3.8vw;
  padding: 5px;
  position: absolute;
  text-transform: uppercase;
  text-shadow: 3px 3px 3px #000;
}

</style>
