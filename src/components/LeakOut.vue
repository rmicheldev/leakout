<template>
  <div>LEAK OUT</div>
  <div>{{silhueta}}</div>
  <button @click="iniciar">iniciar</button>
</template>


<script>
export default{
  data: () => ({
    // silhueta: [5, 4, 3, 2, 1, 2, 3, 4, 5],
    // silhueta: [7, 10, 2, 5, 13, 3, 4, 10, 5, 9, 4, 2, 6, 5, 18, 6, 8, 6, 15, 4, 20, 4, 8, 9, 5, 21, 4, 7, 19, 2],
    silhueta: [7, 10, 2, 5, 13, 3, 4, 1, 5, 9],
    aguaNoCopo: 0,
  }),
  methods: {
    iniciar() {
      console.log("E lá vamos nós");
      let indice = 0;
      this.aguaNoCopo = 0;
      while(indice < this.silhueta.length){
        console.log("Minha altura é", this.silhueta[indice]);
        let menorEsquerda = this.meuMaiorVizinhoDaEsquerda(indice, this.silhueta[indice]);
        let menorDireita = this.meuMaiorVizinhoDaDireita(indice, this.silhueta[indice]);
        let marcaDAgua = Math.min(menorDireita, menorEsquerda);
        console.log("Marca da agua", marcaDAgua);
        let gotasDeAgua = Math.max( (marcaDAgua - this.silhueta[indice]), 0);
        console.log(gotasDeAgua);
        this.aguaNoCopo += gotasDeAgua;
        console.log("Próximo");
        indice++;
      }
      console.log("Sobrou", this.aguaNoCopo);
    },
    meuMaiorVizinhoDaEsquerda(indice, valor){
      let esquerda = this.silhueta.slice(0, indice);
      // console.log("Na esqueda", esquerda);
      let maiorEsquerda = Math.max(...esquerda, 0)

      console.log("Maior na esqueda", maiorEsquerda);
      return maiorEsquerda;
    },
    meuMaiorVizinhoDaDireita(indice, valor){
      let direita = this.silhueta.slice(indice, this.silhueta.length);
      // console.log("Na direita", direita);
      let maiorDireita = Math.max(...direita, 0)

      console.log("Maior na direita", maiorDireita);
      return maiorDireita;
    }

  }
};
</script>
<style scoped>
a {
  color: #42b983;
}
</style>
