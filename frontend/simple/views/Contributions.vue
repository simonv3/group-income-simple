<template>
  <mask-to-modal tag="main">
    <div class="section is-hero level">
      <div>
        <i18n tag="h1" class="title is-1 is-marginless">
          Contributions
        </i18n>
        <i18n tag="p">See everyone’s contributions to the group and manage yours.</i18n>
      </div>
      <div>
        <groups-min-income :group="currentGroupState" />
      </div>
    </div>

    <section class="section columns is-desktop is-multiline c-grid">
      <div class="column">
        <i18n tag="h2" class="title is-3">Receiving</i18n>

        <ul class="c-ul">
          <contribution v-for="contribution in fakeStore.receiving.nonMonetary">
            <span v-html="textReceivingNonMonetary(contribution)"></span>
            <text-who :who="contribution.who"></text-who>
          </contribution>

          <trigger>
            <contribution v-if="doesReceiveMonetary" variant="editable" isMonetary @interaction="handleFormTriggerClick">
              <span v-html="textReceivingMonetary(fakeStore.receiving.monetary)"></span>
              <text-who :who="fakeStore.groupMembersPledging"></text-who>
              <i18n>each month</i18n>
            </contribution>
          </trigger>
        </ul>
      </div>

      <div class="column">
        <i18n tag="h2" class="title is-3">Giving</i18n>

        <ul class="c-ul">
          <contribution v-for="contribution, index in fakeStore.giving.nonMonetary"
            class="has-text-weight-bold"
            variant="editable"
            @new-value="(value) => handleEditNonMonetary(value, index)"
          >{{contribution}}</contribution>

          <contribution variant="unfilled" @new-value="submitAddNonMonetary">
            <i class="fa fa-heart c-contribution-icon" aria-hidden="true"></i>
            <i18n>Add a non-monetary method</i18n>
          </contribution>

          <trigger>
            <contribution v-if="doesGiveMonetary" variant="editable" isMonetary @interaction="handleFormTriggerClick">
              <i18n class="has-text-weight-bold" :args="{amount:`${fakeStore.currency}${fakeStore.giving.monetary}`}">Pledging up to {amount}</i18n><i18n>to other's mincome</i18n>
              <i18n tag="p" class="is-size-7" v-if="fakeStore.giving.monetary == 0" :args="{amount: '[$170]'}">(The group's average pledge is {amount})</i18n>
            </contribution>
          </trigger>
        </ul>
      </div>
    </section>

    <trigger>
      <message-missing-income class="section"
        v-if="fakeStore.isFirstTime && !ephemeral.isEditingIncome"
        @click="handleFormTriggerClick"
      ></message-missing-income>
    </trigger>

    <target>
      <income-form ref="incomeForm"
        v-if="ephemeral.isEditingIncome"
        @save="handleIncomeSave"
        @cancel="handleIncomeCancel"
      ></income-form>
    </target>

    <masker :isActive="ephemeral.isEditingIncome"></masker>
  </mask-to-modal>
</template>
<style lang="scss" scoped>
@import "../assets/sass/theme/index";

.c-grid .column {
  @include touch {
    padding-left: 0;
    padding-right: 0;
  }

  @include desktop {
    &:first-child {
      padding-left: 0;
    }

    &:nth-child(2) {
      padding-right: 0;
    }
  }
}

.c-ul {
  margin: $gi-spacer*0.75 0;
}
</style>
<script>
import { mapGetters } from 'vuex'
import currencies from './utils/currencies.js'
import MessageMissingIncome from './containers/contributions/MessageMissingIncome.vue'
import IncomeForm from './containers/contributions/IncomeForm.vue'
import GroupsMinIncome from './components/GroupsMinIncome.vue'
import Contribution from './components/Contribution.vue'
import TextWho from './components/TextWho.vue'
import { MaskToModal, Trigger, Target, Masker } from './components/Transitions/MaskToModal/index.js'

export default {
  name: 'Contributions',
  components: {
    GroupsMinIncome,
    Contribution,
    TextWho,
    IncomeForm,
    MessageMissingIncome,
    MaskToModal,
    Trigger,
    Target,
    Masker
  },
  data () {
    return {
      ephemeral: {
        isEditingIncome: false
      },
      // -- Hardcoded Data just for layout purposes:
      fakeStore: {
        currency: currencies['USD'],
        isFirstTime: true, // true when user doesn't have any income details. It displays the 'Add Income Details' box
        mincome: 500,
        receiving: {
          nonMonetary: [
            {
              what: 'Cooking',
              who: 'Lilia Bouvet'
            },
            {
              what: 'Cuteness',
              who: ['Zoe Kim', 'Laurence E']
            }
          ],
          monetary: null // Number - You can edit to see the receiving monetary contribution box (change isFirstTime to false too).
        },
        giving: {
          nonMonetary: [], // ArrayOf(String)
          monetary: null // Number - You can eddit to see the giving monetary contribution box (change isFirstTime to false too).
        },
        groupMembersPledging: [
          'Jack Fisher',
          'Charlotte Doherty',
          'Thomas Baker',
          'Francisco Scott'
        ]
      }
    }
  },
  computed: {
    ...mapGetters([
      'currentGroupState'
    ]),
    doesReceiveMonetary () {
      return !!this.fakeStore.receiving.monetary && !this.ephemeral.isEditingIncome
    },
    doesGiveMonetary () {
      return !!this.fakeStore.giving.monetary && !this.ephemeral.isEditingIncome
    }
  },
  methods: {
    textReceivingNonMonetary (contribution) {
      return this.L('<strong>{what}</strong> from', { what: contribution.what })
    },
    textReceivingMonetary (contribution) {
      return this.L('<strong>Up to {amount} for mincome</strong> from', {
        amount: `${this.fakeStore.currency}${contribution}`
      })
    },

    submitAddNonMonetary (value) {
      console.log('TODO $store - submitAddNonMonetary')
      this.fakeStore.giving.nonMonetary.push(value) // Hardcoded Solution
    },
    handleEditNonMonetary (value, index) {
      if (!value) {
        console.log('TODO $store - deleteNonMonetary')
        this.fakeStore.giving.nonMonetary.splice(index, 1) // Hardcoded Solution
      } else {
        console.log('TODO $store - editNonMonetary')
        this.$set(this.fakeStore.giving.nonMonetary, index, value) // Hardcoded Solution
      }
    },
    handleFormTriggerClick () {
      this.ephemeral.isEditingIncome = true
    },
    handleIncomeSave ({ canPledge, amount }) {
      console.log('TODO $store - Save Income Details')
      // -- Hardcoded Solution
      this.fakeStore.receiving.monetary = canPledge ? null : this.fakeStore.mincome - amount
      this.fakeStore.giving.monetary = canPledge ? amount : null
      this.fakeStore.isFirstTime = false

      this.closeIncome()
    },
    handleIncomeCancel () {
      this.closeIncome()
    },
    closeIncome () {
      this.ephemeral.isEditingIncome = false
    }
  }
}
</script>
