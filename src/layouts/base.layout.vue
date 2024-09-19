<script lang="ts" setup>
import {NIcon, useThemeVars} from 'naive-ui';
import { Home2, Menu2} from '@vicons/tabler';
import {storeToRefs} from 'pinia';
import MenuLayout from '../components/MenuLayout.vue';
import {useStyleStore} from '@/stores/style.store';
import {config} from '@/config';
import type {ToolCategory} from '@/tools/tools.types';
import {useToolStore} from '@/tools/tools.store';
import CollapsibleToolMenu from '@/components/CollapsibleToolMenu.vue';

const styleStore = useStyleStore();
const version = config.app.version;
const commitSha = config.app.lastCommitSha.slice(0, 7);

const {t} = useI18n();

const toolStore = useToolStore();
const {favoriteTools, toolsByCategory} = storeToRefs(toolStore);

const tools = computed<ToolCategory[]>(() => [
  ...(favoriteTools.value.length > 0 ? [{
    name: t('tools.categories.favorite-tools'),
    components: favoriteTools.value
  }] : []),
  ...toolsByCategory.value,
]);
</script>

<template>
  <MenuLayout class="menu-layout" :class="{ isSmallScreen: styleStore.isSmallScreen }">
    <template #sider>
      <div class="hero-wrapper-title">
        IT - TOOLS
      </div>

      <div class="sider-content">
        <CollapsibleToolMenu :tools-by-category="tools"/>

        <div class="footer">
          <div>
            由以下开源项目制作而成
          </div>
          <div>
            IT-Tools

            <c-link target="_blank" rel="noopener" :href="`https://github.com/CorentinTh/it-tools/tree/v${version}`">
              v{{ version }}
            </c-link>

            <template v-if="commitSha && commitSha.length > 0">
              -
              <c-link
                  target="_blank"
                  rel="noopener"
                  type="primary"
                  :href="`https://github.com/CorentinTh/it-tools/tree/${commitSha}`"
              >
                {{ commitSha }}
              </c-link>
            </template>
          </div>
          <div>
            © {{ new Date().getFullYear() }}
            <c-link target="_blank" rel="noopener" href="https://github.com/CorentinTh">
              Corentin Thomasset
            </c-link>
          </div>
        </div>
      </div>
    </template>

    <template #content>
      <div flex items-center justify-center gap-2>
        <c-button
            circle
            variant="text"
            :aria-label="$t('home.toggleMenu')"
            @click="styleStore.isMenuCollapsed = !styleStore.isMenuCollapsed"
        >
          <NIcon size="25" :component="Menu2"/>
        </c-button>

        <c-tooltip :tooltip="$t('home.home')" position="bottom">
          <c-button to="/" circle variant="text" :aria-label="$t('home.home')">
            <NIcon size="25" :component="Home2"/>
          </c-button>
        </c-tooltip>

        <c-tooltip :tooltip="$t('home.uiLib')" position="bottom">
          <c-button v-if="config.app.env === 'development'" to="/c-lib" circle variant="text"
                    :aria-label="$t('home.uiLib')">
            <icon-mdi:brush-variant text-20px/>
          </c-button>
        </c-tooltip>

        <command-palette/>
      </div>
      <slot/>
    </template>
  </MenuLayout>
</template>

<style lang="less" scoped>
// ::v-deep(.n-layout-scroll-container) {
//     @percent: 4%;
//     @position: 25px;
//     @size: 50px;
//     @color: #eeeeee25;
//     background-image: radial-gradient(@color @percent, transparent @percent),
//         radial-gradient(@color @percent, transparent @percent);
//     background-position: 0 0, @position @position;
//     background-size: @size @size;
// }

.support-button {
  background: rgb(37, 99, 108);
  background: linear-gradient(48deg, rgba(37, 99, 108, 1) 0%, rgba(59, 149, 111, 1) 60%, rgba(20, 160, 88, 1) 100%);
  color: #fff !important;
  transition: padding ease 0.2s !important;

  &:hover {
    color: #fff;
    padding-left: 30px;
    padding-right: 30px;
  }
}

.footer {
  text-align: center;
  color: #838587;
  margin-top: 20px;
  padding: 20px 0;
}

.sider-content {
  padding-top: 60px;
  padding-bottom: 200px;
}

.hero-wrapper-title {
  position: absolute;
  display: flex;
  align-items: center;
  justify-content: center;
  left: 0;
  width: 100%;
  z-index: 10;
  overflow: hidden;
  font-size: 25px;
  font-weight: 600;
  height: 60px;
  background-color: white;
}
</style>
