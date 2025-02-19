<script setup>
import { ref, watch, toRefs, defineProps, defineEmits } from 'vue';
import { clone } from '@/helpers/utils';
import pluginsObj from '@snapshot-labs/snapshot.js/src/plugins';
import pluginsConfig from '@/components/Plugin/config.json';

const props = defineProps({
  open: Boolean,
  modelValue: Object,
  space: Object,
  proposal: Object
});

const emit = defineEmits(['close', 'update:modelValue']);

const { open } = toRefs(props);
const plugins = ref([]);
const selected = ref(false);
const form = ref({});

function getLogoUrl(plugin) {
  return `https://raw.githubusercontent.com/snapshot-labs/snapshot.js/master/src/plugins/${plugin}/logo.png`;
}

function showButton(key) {
  const pluginsWithParams = Object.keys(props.space.plugins).filter(
    plugin => pluginsConfig[plugin].proposalParams
  );
  return pluginsWithParams.map(plugin => plugin).includes(key);
}

if (props.space.plugins) {
  plugins.value = Object.fromEntries(
    Object.keys(props.space.plugins).map(plugin => {
      const instance = new pluginsObj[plugin]();
      return [plugin, instance];
    })
  );
}

watch(open, () => {
  if (props.modelValue && props.open) form.value = clone(props.modelValue);
  selected.value = false;
});

watch(selected, value => {
  if (value === 'safeSnap') {
    form.value.safeSnap = form.value.safeSnap || {};
    emit('update:modelValue', form.value);
    emit('close');
  }
});
</script>

<template>
  <UiModal :open="open" @close="$emit('close')">
    <template v-slot:header>
      <h3>{{ $t('plugins') }}</h3>
    </template>
    <div class="m-4 mt-4" v-if="selected === false">
      <div
        v-for="(plugin, key) in plugins"
        :key="key"
        class="mb-3 p-4 border rounded-2 link-color text-center"
      >
        <img
          class="circle border"
          :src="getLogoUrl(key)"
          width="64"
          height="64"
        />
        <h3 v-text="plugin.name" />
        <div v-if="plugin.website" class="mb-2">
          <a :href="plugin.website" target="_blank" class="link-color">
            {{ $t('learnMore') }}
            <Icon name="external-link" />
          </a>
        </div>
        <UiButton v-if="showButton(key)" @click="selected = key">
          {{ !form[key] ? $t('add') : $t('edit') }}
        </UiButton>
      </div>
    </div>
    <template v-if="selected === false" v-slot:footer>
      <div class="col-6 float-left pr-2">
        <UiButton @click="$emit('close')" type="button" class="width-full">
          {{ $t('cancel') }}
        </UiButton>
      </div>
      <div class="col-6 float-left pl-2">
        <UiButton
          @click="$emit('update:modelValue', form), $emit('close')"
          type="submit"
          class="width-full button--submit"
        >
          {{ $t('save') }}
        </UiButton>
      </div>
    </template>
    <div v-if="selected !== false" class="m-4 p-4 border rounded-2 link-color">
      <PluginAragonConfig
        :proposal="proposal"
        v-model="form.aragon"
        @close="selected = false"
        v-if="selected === 'aragon'"
      />
      <PluginGnosisConfig
        :proposal="proposal"
        :network="space.network"
        v-model="form.gnosis"
        @close="selected = false"
        v-if="selected === 'gnosis'"
      />
    </div>
  </UiModal>
</template>
