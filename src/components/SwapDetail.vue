<template>
  <q-card class="q-mb-md text-black">
    <q-card-section horizontal class="justify-between">
      <q-card-section>
        <div class="text-h6">
        <q-tooltip>
          {{swap.content.source.amount}} ALEPH tokens from {{swap.content.source.chain}} to {{swap.content.target.chain}}
        </q-tooltip>
        <currency-amount :amount="swap.content.source.amount" :symbol="swap.content.symbol" />
        </div>
        <div class="text-subtitle2">
          <q-tooltip>
          {{swap.time * 1000 | moment("lll")}}
          </q-tooltip>
          {{swap.time * 1000 | moment("from")}}
        </div>
      </q-card-section>

      <q-card-section class="col text-right">
        {{swap.content.source.chain}}<br />
        <Address :address="swap.content.source.address" :chain="swap.content.source.chain" /><br />
        <span v-if="swap.content.source.height" class="text-caption">Block {{swap.content.source.height}}</span>
      </q-card-section>


      <q-card-section>
        <q-icon name="double_arrow" />
      </q-card-section>

      <q-card-section class="col">
        {{swap.content.target.chain}}<br />
        <Address :address="swap.content.target.target" :chain="swap.content.target.chain" /><br />
        <span v-if="swap.content.target.height" class="text-caption">Block {{swap.content.target.height}}</span>
      </q-card-section>

      <q-card-section class="col-auto column justify-between">
        <div class="text-center">
          <q-tooltip>
          {{swap.content.status}}
          </q-tooltip>
          <q-icon v-if="swap.content.status == 'pending'" name="schedule" />
          <q-icon class="text-negative" v-else-if="swap.content.status == 'failed'" name="warning" />
          <q-icon class="text-secondary" v-else-if="swap.content.status == 'finished'" name="done" />
          <q-icon v-else name="help" />
        </div>
        <div>
          <q-btn
            round
            flat
            dense
            :icon="expanded ? 'keyboard_arrow_up' : 'keyboard_arrow_down'"
            @click="expanded = !expanded"
          />
        </div>
      </q-card-section>
    </q-card-section>
    <q-slide-transition>
        <div v-show="expanded">
          <q-separator />
          <q-card-section class="container">
            <div v-if="swap.content.source.tx" class="text-subtitle2">
              Tx from:<br />
              <tx-hash :hash="swap.content.source.tx" :chain="swap.content.source.chain" />
            </div>
            <div v-if="swap.content.target.tx" class="text-subtitle2">
              Tx to:<br />
              <tx-hash :hash="swap.content.target.tx" :chain="swap.content.target.chain" />
            </div>

          </q-card-section>
        </div>
      </q-slide-transition>
  </q-card>
</template>
<script>
import Address from './Address'
import CurrencyAmount from './CurrencyAmount'
import TxHash from './TxHash'
export default {
  name: 'SwapDetail',
  props: ['swap'],
  components: {
    Address,
    CurrencyAmount,
    TxHash
  },
  data() {
    return {
      expanded: false
    }
  }
}
</script>
<style scoped>
span {
    display: inline-block;
    max-width: 100%;
    overflow: hidden;
    text-overflow: ellipsis;
}
.container {
  background-color: rgb(245, 245, 245);
}
</style>
