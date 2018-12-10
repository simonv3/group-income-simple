<template>
  <proposal-template
    :subTitle="L('Change minimum income')"
    :onSubmit="handleSubmit">

    <div class="field has-addons">
      <p class="control">
        <a class="button is-static">
          {{ currency }} {{ currencyShortCode }}
        </a>
      </p>
      <p class="control is-expanded">
        <input class="input" type="text" :placeholder="L('New amount')">
      </p>
    </div>
    <article class="message is-info columns">
      <!-- REVIEW is it worth turning this into a component? -->
      <div class="column is-1 icon has-text-centered">
        <i class="fa fa-info-circle has-text-info"></i>
      </div>
      <div class="column is-11 message-body">
        The current minimum income is <strong>{{ mincome }}</strong>.
        According to your voting rules, <strong>{{ thresholdUsers }}</strong> members will have to agree with this change.
      </div>
    </article>
  </proposal-template>
</template>
<script>
import ProposalTemplate from './ProposalTemplate.vue'
import currencies from '../../utils/currencies.js'

export default {
  name: 'Mincome',
  components: { ProposalTemplate },
  props: {
    modalData: Object,
    validator: (value) => {
      return value.hasOwnProperty('group')
    }
  },
  computed: {
    countOfMembers: function () {
      return Object.keys(this.modalData.group.profiles).length
    },
    thresholdUsers: function () {
      // REVIEW This should probably be rounded up?
      return this.modalData.group.memberApprovalThreshold * this.countOfMembers
    },
    mincome: function () {
      return this.currency + this.modalData.group.incomeProvided
    },
    currencyShortCode: function () {
      return this.modalData.group.incomeCurrency
    },
    currency: function () {
      return `${currencies[this.currencyShortCode]}`
    }
  },
  methods: {
    handleSubmit () {
      console.log('TODO: Logic to Propose a new mincome')
    }
  }
}
</script>
