<script setup lang="ts">
import { useNotification } from 'naive-ui'
import { storeToRefs } from 'pinia'

import { Ping } from '@/api'
import { getGitHubTemplates } from '@/api/github'
import router from '@/router'
import { useSettingStore } from '@/store/setting'
import List from '@/views/List.vue'

const { templateRepo, templates, githubToken } = storeToRefs(useSettingStore())

const notification = useNotification()

onMounted(() => {
  Ping()
    .then((res) => {
      if (!res.success) {
        router.push('/setting')
        notification['error']({
          content: 'Server is not available',
          meta: res.error?.message || 'Unknown error',
          duration: 2500,
          keepAliveOnHover: true,
        })
      }
    })
    .catch((error) => {
      router.push('/setting')
      notification['error']({
        content: 'Server is not available',
        meta: String(error) || 'Unknown error',
      })
    })

  getGitHubTemplates(templateRepo.value, githubToken.value)
    .then((res) => {
      templates.value = res
    })
    .catch((error) => {
      console.log(error)
    })
})
</script>

<template>
  <List />
</template>

<style scoped></style>
