<template>
  <SmartModal
    v-if="show"
    :title="`${$t('environment.new')}`"
    @close="hideModal"
  >
    <template #body>
      <div class="flex flex-col px-2">
        <input
          id="selectLabelEnvAdd"
          v-model="name"
          v-focus
          class="input floating-input"
          placeholder=" "
          type="text"
          autocomplete="off"
          @keyup.enter="addNewEnvironment"
        />
        <label for="selectLabelEnvAdd">
          {{ $t("action.label") }}
        </label>
      </div>
    </template>
    <template #footer>
      <span>
        <ButtonPrimary
          :label="`${$t('action.save')}`"
          @click.native="addNewEnvironment"
        />
        <ButtonSecondary
          :label="`${$t('action.cancel')}`"
          @click.native="hideModal"
        />
      </span>
    </template>
  </SmartModal>
</template>

<script lang="ts">
import { defineComponent } from "@nuxtjs/composition-api"
import { useReadonlyStream } from "~/helpers/utils/composables"
import { createEnvironment, environments$ } from "~/newstore/environments"

export default defineComponent({
  props: {
    show: Boolean,
  },
  setup() {
    return {
      envList: useReadonlyStream(environments$, []),
    }
  },
  data() {
    return {
      name: null as string | null,
    }
  },
  methods: {
    addNewEnvironment() {
      if (!this.name) {
        this.$toast.error(`${this.$t("environment.invalid_name")}`)
        return
      }
      createEnvironment(this.name)
      // TODO: find better way to get index of new environment
      this.$emit("environment-added", {
        name: this.name,
        index: this.envList.length - 1,
      })
      this.hideModal()
    },
    hideModal() {
      this.name = null
      this.$emit("hide-modal")
    },
  },
})
</script>
