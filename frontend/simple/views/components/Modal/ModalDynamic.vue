<template>
  <modal-basic :isActive="ephemeral.isActive" @close="closeModal">
    <component
      :is="ephemeral.activeModal"
      v-bind:modal-data="ephemeral.modalData"></component>
  </modal-basic>
</template>
<script>
import ModalBasic from './ModalBasic.vue'
import sbp from '../../../../../shared/sbp.js'
import { OPEN_MODAL, CLOSE_MODAL } from '../../../utils/events.js'

export default {
  name: 'ModalDynamic',
  components: {
    ModalBasic
  },
  data () {
    return {
      ephemeral: {
        activeModal: null,
        isActive: null,
        modalData: null
      }
    }
  },
  created () {
    sbp('okTurtles.events/on', OPEN_MODAL, (component, ...data) => {
      // Is there a way to "type" data to make sure it's the right format?
      return this.openModal(component, data)
    })
    sbp('okTurtles.events/on', CLOSE_MODAL, this.closeModal)
  },
  methods: {
    openModal (component, modalData) {
      this.ephemeral.activeModal = component
      this.ephemeral.isActive = true

      if (modalData && Array.isArray(modalData)) {
        this.ephemeral.modalData = modalData[0]
      }
    },
    closeModal () {
      this.ephemeral.isActive = false
    }
  }
}
</script>
