<template>
  <div>
    <h1 class="centralizado">{{ titulo }}</h1>
    <p v-show="mensagem" class="centralizado">{{ mensagem }}</p>
    <input type="search" 
      class="filtro" 
      @input="filtro = $event.target.value" 
      placeholder="filtre pelo título">
    <ul class="lista-fotos">
      <li class="lista-fotos-item" v-for="foto of fotosComFiltro" :key="foto.url">
        <meu-painel :titulo="foto.titulo">
          <imagem-responsiva v-meu-transform:scale.animate="1.2" :url="foto.url" :titulo="foto.titulo" />
          <router-link :to="{ name: 'altera', params: { id: foto._id } }">
            <meu-botao tipo="button" titulo="ALTERAR" />
          </router-link>
          <meu-botao 
            tipo="button" 
            titulo="REMOVER"
            :confirmacao="true"
            estilo="perigo"
            @botaoAtivado="remove(foto)"
          />
        </meu-painel>
      </li>
    </ul>
  </div>
</template>

<script>
import Painel from '../shared/Painel/index.vue';
import ImagemResponsiva from '../shared/ImagemResponsiva/index.vue';
import Botao from '../shared/Botao/index.vue';
import Transform from '../../directives/Transform';
import FotoService from '../../domain/Foto/service';

export default {
  components: {
    'meu-painel': Painel,
    'imagem-responsiva': ImagemResponsiva,
    'meu-botao' : Botao
  },

  directives: {
    'meu-transform': Transform
  },

  data() {

    return {

      titulo: 'Alurapic', 
      fotos: [],
      filtro: '',
      mensagem: ''
    }
  },

  computed: {
    fotosComFiltro() {
      if(this.filtro) {
        let exp = new RegExp(this.filtro.trim(), 'i');
        return this.fotos.filter(foto => exp.test(foto.titulo));
      } else {
        return this.fotos;
      }
    }
  },

  methods: {
    remove(foto) {
      this.service.apaga(foto._id)
        .then(() => {
          this.fotos.splice(this.fotos.indexOf(foto), 1);
          this.mensagem = 'Foto removida com sucesso'
        }, err => this.mensagem = err.message);  
    }
  },

  created() {
    this.service = new FotoService(this.$resource);
    this.service.lista()
      .then(fotos => this.fotos = fotos, err => this.mensagem = err.message);
  }
}

</script>

<style>
  .centralizado {
    text-align: center;
  }

  .lista-fotos {
    list-style: none;
  }

  .lista-fotos .lista-fotos-item {
    display: inline-block;
  }

  .filtro {
    display: block;
    width: 100%;
  }
</style>
