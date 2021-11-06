<template>
  <div style="font-size:2em;padding-bottom: 1em;">LEAK OUT</div>
  <div v-if="!arquivoCarregado">
    <label style="padding: 0 1em" for="files" class="btn">Selecione o arquivo:</label>
    <input type="file" ref="file" id="files" @change="lerArquivo"  />
  </div>

  <div v-if="arquivoCarregado">
    <div style="padding: 1em;">
      O arquivo carregado possui {{silhuetas.tamanho}} registro(s)
    </div>
    <div v-for="(registro, indice) in silhuetas.registros" :key="indice" style="text-align: left;">
      <div style="padding-top: 1em;">
        Registro {{(indice+1)}} possui {{registro.tamanho}} silhuetas
      </div>
      <span style="border: solid #000 1px; margin: 1em; padding: 0 1em">
        <span v-for="(silhueta, subindice) in registro.conteudo" :key="subindice" style="padding: 0.5em">
          {{silhueta}}
        </span>
      </span>
      <button @click="calcular(indice)">Calcular</button> 
      <span style="border: 1px solid #000; margin: 1em; padding: 0px 1em;font-size: 2em;font-weight: 800;">
        {{registro.resultado}}
      </span>
    </div>
    <div style="margin: 2em;color: #f00;"> Utilize F5 para reiniciar </div>
  </div>
</template>


<script>
export default{
  data: () => ({
    arquivoCarregado: false,
    silhuetas: {},
  }),
  methods: {
    lerArquivo(event) {
      // Busca a interração do usuário
      let arquivo = event.target.files[0];
      if(!arquivo || arquivo.type !== 'text/plain') {
        console.error("Arquivo não suportado");
        return;
      }

      // Lê o arquivo
      let reader = new FileReader();
      reader.readAsText(arquivo, "UTF-8");
      reader.onload = evt => {
        let conteudo = evt.target.result;
        let linhas = conteudo.match(/[^\r\n]+/g);
        if(linhas.length <=0 ){
          console.error("Arquivo vazio");
          return;
        }

        let itens = [];
        let chunkSize = 2;

        // Quebra as linha por pares, [tamanho, silhueta]

        for (let indice = 1; indice < linhas.length; indice += chunkSize){
          let itemLinha = linhas.slice(indice, indice + chunkSize);
          itens.push({tamanho: itemLinha[0], conteudo: itemLinha[1].split(" "), resultado: "-"})
        }
        this.silhuetas = {tamanho: linhas[0], registros: itens }
        this.arquivoCarregado = true;

      }
      reader.onerror = evt => {
        console.error(evt);
      }
    },

    calcular(itemSilhueta) {
      console.log(itemSilhueta);

      let indice = 0;
      let aguaNoCopo = 0;
      let item = this.silhuetas.registros[itemSilhueta].conteudo;

      while(indice < item.length) {
        // Verifica os extremos de cada lado
        let menorEsquerda = this.meuMaiorVizinhoDaEsquerda(item, indice, item[indice]);
        let menorDireita = this.meuMaiorVizinhoDaDireita(item, indice, item[indice]);
        // entre os extremos, pega o menos
        let marcaDAgua = Math.min(menorDireita, menorEsquerda);
        
        // desconta a silhueta da altura "marca da agua". Se for negativo, assume 0
        let gotasDeAgua = Math.max( (marcaDAgua - item[indice]), 0);
        // soma
        aguaNoCopo += gotasDeAgua;
        // caminha
        indice++;
      }
      this.silhuetas.registros[itemSilhueta].resultado = aguaNoCopo;
    },
    
    meuMaiorVizinhoDaEsquerda(item, indice, valor){
      let esquerda = item.slice(0, indice);
      // console.log("Na esqueda", esquerda);
      let maiorEsquerda = Math.max(...esquerda, 0)

      // console.log("Maior na esqueda", maiorEsquerda);
      return maiorEsquerda;
    },
    meuMaiorVizinhoDaDireita(item, indice, valor){
      let direita = item.slice(indice, item.length);
      // console.log("Na direita", direita);
      let maiorDireita = Math.max(...direita, 0)

      // console.log("Maior na direita", maiorDireita);
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
