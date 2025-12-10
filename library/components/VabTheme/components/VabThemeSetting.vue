<template>
  <el-dropdown class="vab-theme-setting" @visible-change="handleVisibleChange">
    <div class="vabtheme-dropdown">
      <div>{{ translate(active ? '收起' : '展开') }}</div>
      <vab-icon :icon="active ? 'arrow-up-double-line' : 'arrow-down-double-line'" />
    </div>
    <template #dropdown>
      <el-dropdown-menu class="vabtheme-menu">
        <el-dropdown-item v-if="routeName !== 'SeparateLayout'" @click="randomTheme">
          <a>
            <vab-icon icon="fire-line" />
            <p>{{ translate('随机换肤') }}</p>
          </a>
        </el-dropdown-item>
        <el-dropdown-item v-if="routeName !== 'SeparateLayout'" @click="handleOpenTheme">
          <a>
            <vab-icon icon="t-shirt-line" />
            <p>{{ translate('主题配置') }}</p>
          </a>
        </el-dropdown-item>
        <el-dropdown-item @click="changeTheme('technology')">
          <a>
            <vab-icon icon="user-5-line" />
            <p>
              {{ translate('科技主题') }}
            </p>
          </a>
        </el-dropdown-item>
        <el-dropdown-item @click="changeTheme('plain')">
          <a>
            <vab-icon icon="computer-line" />
            <p>
              {{ translate('简洁主题') }}
            </p>
          </a>
        </el-dropdown-item>
        <el-dropdown-item @click="changeTheme('extjs')">
          <a>
            <vab-icon icon="layout-grid-line" />
            <p>
              {{ translate('紧凑主题') }}
            </p>
          </a>
        </el-dropdown-item>
        <el-dropdown-item @click="resetTheme">
          <a>
            <vab-icon icon="arrow-go-back-line" />
            <p>
              {{ translate('默认主题') }}
            </p>
          </a>
        </el-dropdown-item>
        <el-dropdown-item @click="removeLocalStorage">
          <a>
            <vab-icon icon="delete-bin-4-line" />
            <p>
              {{ translate('清理缓存') }}
            </p>
          </a>
        </el-dropdown-item>
      </el-dropdown-menu>
    </template>
  </el-dropdown>
</template>

<script lang="ts" setup>
import { translate } from '/@/i18n'
import { useSettingsStore } from '/@/store/modules/settings'

defineOptions({
  name: 'VabThemeSetting',
})

const settingsStore = useSettingsStore()
const { device, theme } = storeToRefs(settingsStore)
const { saveTheme, updateTheme, setCssVar, updateCaughtTabs } = settingsStore
const active = ref<boolean>(false)
const route = useRoute()
const routeName = ref<any>(route.name)

const handleOpenTheme = () => {
  $pub('shop-vite-open-theme')
}

const removeLocalStorage = () => {
  localStorage.clear()
  updateCaughtTabs(false)
  location.reload()
}

const resetTheme = () => {
  $pub('shop-vite-reset-theme')
}

const changeTheme = (value: string) => {
  $pub('shop-vite-change-theme', value)
  $pub('shop-vite-save-theme')
}

const handleVisibleChange = (value: boolean) => {
  active.value = value
}

const shuffle = (val: any, list: any) => list.filter((item: any) => item !== val)[(Math.random() * (list.length - 1)) | 0]

const randomTheme = async () => {
  const loading = $baseLoading()

  setTimeout(() => {
    const themeName = shuffle(theme.value.themeName, ['default', 'plain', 'technology', 'extjs'])
    const columnStyle = shuffle(theme.value.columnStyle, ['vertical', 'horizontal', 'card', 'arrow', 'semicircle'])
    const tabsBarStyle = shuffle(theme.value.tabsBarStyle, ['card', 'smart', 'smooth', 'rect', 'glass'])
    const showTabsIcon = shuffle(theme.value.showTabsIcon, [true, false])
    const layout =
      device.value === 'desktop'
        ? shuffle(theme.value.layout, ['horizontal', 'vertical', 'column', 'comprehensive', 'fall', 'double'])
        : 'vertical'
    const _color = shuffle(theme.value.color, ['#1e90ff', '#4e6ef2', '#3fb884', '#16baa9', '#009688', '#6954f0', '#f01414'])
    const isFollow = shuffle(theme.value.isFollow, [true, false])

    theme.value.themeName = themeName
    theme.value.columnStyle = columnStyle
    theme.value.tabsBarStyle = tabsBarStyle
    theme.value.showTabsIcon = showTabsIcon
    theme.value.layout = layout

    if (themeName === 'technology') {
      theme.value.color = '#4e88f3'
    } else {
      theme.value.color = _color
    }

    if (themeName === 'default') {
      theme.value.isFollow = isFollow
      theme.value.glassMode = Math.random() > 0.5
    } else {
      theme.value.isFollow = false
    }

    setCssVar()
    updateTheme()
    saveTheme()
    setTimeout(() => {
      loading.close()
      $baseMessage('切换成功', 'success', 'hey')
    }, 1000)
  }, 100)
}

$sub('shop-vite-random-theme', randomTheme)

watch(
  route,
  () => {
    routeName.value = route.name
  },
  { immediate: true }
)
</script>

<style lang="scss" scoped>
.vabtheme-dropdown {
  display: flex;
  align-content: center;
  align-items: center;
  justify-content: center;
  justify-items: center;

  div {
    position: relative;
    display: flex;
    align-content: center;
    align-items: center;
    width: max-content;
    height: 40px;
    margin-left: 6px;
    margin-right: -10px;
    line-height: 40px;
    cursor: pointer;
  }
}

.vabtheme-menu li {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0;
  margin: 0;
  list-style: none;
  text-align: center;

  a {
    display: inline-block;
    width: 60px;
    height: 60px;
    padding-top: 10px;
    text-align: center;
    background: var(--el-color-white);
    border-radius: var(--el-border-radius-base);

    p {
      padding: 0;
      margin: 0;
      overflow: hidden;
      text-overflow: ellipsis;
      font-size: var(--el-font-size-extra-small);
      line-height: 25px;
      white-space: nowrap;
    }
  }
  &:nth-child(n) {
    a {
      &:hover {
        color: var(--el-color-white);
      }
    }
  }

  &:nth-child(1),
  &:nth-child(3),
  &:nth-child(5) {
    a {
      color: var(--el-color-primary);
      background: var(--el-color-primary-light-9);

      &:hover {
        background: var(--el-color-primary);
      }
    }
  }

  &:nth-child(2),
  &:nth-child(6) {
    a {
      color: var(--el-color-success);
      background: var(--el-color-success-lighter);

      &:hover {
        background: var(--el-color-success);
      }
    }
  }

  &:nth-child(4) {
    a {
      color: var(--el-color-info);
      background: var(--el-color-info-lighter);

      &:hover {
        background: var(--el-color-info);
      }
    }
  }

  &:nth-child(7) {
    a {
      color: var(--el-color-danger);
      background: var(--el-color-danger-lighter);

      &:hover {
        background: var(--el-color-danger);
      }
    }
  }
}
</style>
