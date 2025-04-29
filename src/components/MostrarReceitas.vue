<script lang="ts">
import { itensDeLista1EstaoEmLista2 } from '@/operacoes/listas';
import { obterReceitas } from '@/http/index';
import type { PropType } from 'vue';
import BotaoPrincipal from './BotaoPrincipal.vue';
import CardReceita from './CardReceita.vue';
import type IReceita from '@/interfaces/IReceita';

export default {
  name: 'MostrarReceitas',
  props: {
    ingredientes: { type: Array as PropType<string[]>, required: true }
  },
  data() {
    return {
      receitasEncontradas: [] as IReceita[]
    }
  },
  async created() {
    const receitas = await obterReceitas();

    this.receitasEncontradas = receitas.filter((receita) => {
      // Lógica que verifica se posso fazer receita:
      // Todos os ingredientes de uma receita devem estar inclusos na minha lista de ingredientes
      // Se sim, devemos retornar `true`

      const possoFazerReceita = itensDeLista1EstaoEmLista2(receita.ingredientes, this.ingredientes);

      return possoFazerReceita;
    })
  },
  components: { CardReceita, BotaoPrincipal },
  emits: ['voltar']
}
</script>

<template>
  <section class="mostrar-receitas">
    <h1 class="cabecalho titulo-receitas">Receitas</h1>

    <span class="resultados">Resultados encontrados: {{ receitasEncontradas.length }}</span>


    <div v-if="receitasEncontradas.length">
      <span class="apresentacao">Veja as opções de receitas que encontramos com os ingredientes que você tem por
        aí!</span>
      <ul class="receitas">
        <li v-for="receita in receitasEncontradas" :key="receita.nome">
          <CardReceita :receita="receita" />
        </li>
      </ul>
    </div>

    <div v-else class="lista-vazia">
      Ops, não encontramos resultados para sua combinação. Vamos tentar de novo?
      <img src="../assets/images/sem-receitas.png">
    </div>

    <BotaoPrincipal texto="Editar Lista" @click="$emit('voltar')" />
  </section>
</template>


<style scoped>
.mostrar-receitas {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.titulo-receitas {
  color: var(--verde-medio, #3D6D4A);
  display: block;
  margin-bottom: 2rem;
}

.resultados {
  color: var(--verde-medio, #3D6D4A);
  margin-bottom: 1.5rem;
}

.apresentacao {
  display: flex;
  justify-self: center;
  margin-bottom: 2rem;
}

.receitas {
  margin-bottom: 4rem;
  display: flex;
  justify-content: center;
  gap: 1.5rem;
  flex-wrap: wrap;
}

.lista-vazia {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

@media only screen and (max-width: 767px) {
  .dica {
    margin-bottom: 2.5rem;
  }
}
</style>