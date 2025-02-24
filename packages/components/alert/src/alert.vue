<template>
  <transition :name="ns.b('fade')">
    <div
      v-show="visible"
      :class="[ns.b(), ns.m(type), ns.is('center', center), ns.is(effect)]"
      role="alert"
    >
      <el-icon
        v-if="showIcon && iconComponent"
        :class="[ns.e('icon'), isBigIcon]"
      >
        <component :is="iconComponent" />
      </el-icon>
      <div :class="ns.e('content')">
        <span
          v-if="title || $slots.title"
          :class="[ns.e('title'), isBoldTitle]"
        >
          <slot name="title">{{ title }}</slot>
        </span>
        <p v-if="$slots.default || description" :class="ns.e('description')">
          <slot>
            {{ description }}
          </slot>
        </p>
        <template v-if="closable">
          <div
            v-if="closeText"
            :class="[ns.e('close-btn'), ns.is('customed')]"
            @click="close"
          >
            {{ closeText }}
          </div>
          <el-icon v-else :class="ns.e('close-btn')" @click="close">
            <close />
          </el-icon>
        </template>
      </div>
    </div>
  </transition>
</template>
<script lang="ts">
import { defineComponent, computed, ref } from 'vue'
import { ElIcon } from '@element-plus/components/icon'
import { TypeComponents, TypeComponentsMap } from '@element-plus/utils'
import { useNamespace } from '@element-plus/hooks'
import { alertProps, alertEmits } from './alert'

export default defineComponent({
  name: 'ElAlert',

  components: {
    ElIcon,
    ...TypeComponents,
  },

  props: alertProps,
  emits: alertEmits,

  setup(props, { emit, slots }) {
    const ns = useNamespace('alert')
    // state
    const visible = ref(true)
    visible.value = !!visible.value
    // computed
    const iconComponent = computed(
      () => TypeComponentsMap[props.type] || TypeComponentsMap['info']
    )
    const isBigIcon = computed(() =>
      props.description || slots.default ? ns.is('big') : ''
    )
    const isBoldTitle = computed(() =>
      props.description || slots.default ? ns.is('bold') : ''
    )

    // methods
    const close = (evt: MouseEvent) => {
      visible.value = false
      emit('close', evt)
    }

    return {
      ns,
      visible,
      iconComponent,
      isBigIcon,
      isBoldTitle,
      close,
    }
  },
})
</script>
