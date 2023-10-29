<template>
  <q-page padding>
    <q-form @submit="onSubmit" class="row q-col-gutter-lg">

      <q-input outlined v-model="form.title" label="Título" lazy-rules class="col-lg-6 col-xs-12"
        :rules="[val => val && val.length > 0 || 'Campo obrigatório']" />

      <q-input outlined v-model="form.author" label="Autor" lazy-rules class="col-lg-6 col-xs-12"
        :rules="[val => val && val.length > 0 || 'Campo obrigatório']" />

      <div class="col-lg-12 col-xs-12">
        <q-editor v-model="form.content" min-height="5rem"
          :rules="[val => val && val.length > 0 || 'Campo obrigatório']" />
      </div>

      <div class="col-12 q-gutter-sm">
        <q-btn label="Salvar" color="primary" class="float-right" icon="save" type="submit" />
        <q-btn label="Cancelar" color="white" class="float-right" text-color="primary" :to="{ name: 'home' }" />
      </div>

    </q-form>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import postsService from 'src/services/posts'
import { useQuasar } from 'quasar'
import { useRouter, useRoute } from 'vue-router'

export default defineComponent({
  name: 'FormPost',

  setup () {
    const { post, getById, update } = postsService()
    const $q = useQuasar()
    const router = useRouter()
    const route = useRoute()
    const form = ref({
      title: '',
      content: '',
      author: ''
    })

    onMounted(() => {
      if (route.params.id) {
        getPostById(route.params.id)
      }
    })

    const onSubmit = async () => {
      try {
        let textModify
        if (form.value.id) {
          textModify = 'Atualizado'
          await update(form.value)
        } else {
          textModify = 'Adicionado'
          await post(form.value)
        }
        $q.notify({ message: `Artigo ${textModify} com sucesso!`, icon: 'check', color: 'positive' })
        router.push({ name: 'home' })
      } catch (error) {
        $q.notify({ message: 'Erro ao Enviar o Artigo!', icon: 'warning', color: 'negative' })
      }
    }

    const getPostById = async (id) => {
      try {
        const response = await getById(id)
        form.value = response
      } catch (error) {
        $q.notify({ message: 'Dados não existentes!', icon: 'times', color: 'negative' })
      }
    }

    return {
      form,
      onSubmit,
      getPostById
    }
  }
})
</script>

<style></style>
