<template>
  <div>
    <q-card label="Swap">
      <q-card-section class="q-pb-none">
        <div class="text-h6">Swap tokens</div>
      </q-card-section>
      <q-card-section horizontal class="justify-between" v-if="!out_txid">
        <q-card-section class="col">
          <q-select v-model="source_chain" :options="source_chains" label="Source chain" />
          <div v-if="source_chain == 'NEO'">
            <div v-if="(source_account==null)||(source_account.type != 'NEO')">
              Login with:
              <br />
              <q-btn color="primary" size="sm" @click="login_o3"><img src="../assets/img/o3.png" height="24px"></q-btn>
            </div>
            <div v-else>
              <q-field label="Address" stack-label>
                <template v-slot:control>
                  <div class="self-center full-width no-outline" tabindex="0">{{source_account.address}}</div>
                </template>
              </q-field>
              <q-input bottom-slots v-model.number="amount" label="Amount" type="number">
                <template v-slot:hint>
                  Current balance: {{aleph_balance}}
                </template>
              </q-input>
            </div>
          </div>
          <div v-else-if="source_chain == 'NULS2'">
            <q-input bottom-slots v-model.number="amount" label="Amount" type="number">
            </q-input>
          </div>
        </q-card-section>
        <q-card-section class="self-center">
          <q-icon name="double_arrow" />
        </q-card-section>
        <q-card-section class="col">
          <q-select v-model="target_chain" :options="target_chains" label="Target chain" />
          <q-input v-model="target_address" label="Target address" :error="check_address()" />
          <q-field label="Amount" stack-label>
            <template v-slot:control>
              <div class="self-center full-width no-outline" tabindex="0">{{amount}}</div>
            </template>
          </q-field>
        </q-card-section>
      </q-card-section>
      <q-card-section class="text-right" v-if="(source_account.address != undefined)&&!check_address()&&!out_txid">
        <q-btn @click="do_swap" color="primary">
          Swap !
        </q-btn>
      </q-card-section>
      <q-card-section class="text-center" v-else-if="(source_chain == 'NULS2')&&!check_address()&&!out_txid">
        To proceed, please send <strong>{{amount}}</strong> ALEPH<br />
        to <strong>{{targets.NULS2}}</strong><br/>
        with remark <strong>{{target_address}}</strong>
      </q-card-section>
      <q-card-section v-if="out_txid">
        Transaction issued:<br />
        <tx-hash :hash="out_txid" :chain="source_chain" />
      </q-card-section>
    </q-card>
    <div class="row">
      <div class="col">
      </div>
      <div class="col-auto">
      </div>
      <div class="col align-right">
      </div>
    </div>
  </div>
</template>
<script>
import neoDapi from 'neo-dapi';
import {
  get_nuls_balance_info, get_ethereum_balance_info, get_neo_balance_info
} from '../services/balances'

import TxHash from './TxHash'

export default {
  name: 'Swap',
  props: [],
  components: {
    TxHash
  },
  data() {
    return {
      source_chain: 'NULS2',
      source_account: {},
      source_balances: {},
      target_chain: 'ETH',
      target_address: '',
      out_txid: null,
      amount: 0,
      source_chains: [
        'NULS2',
        'NEO'
      ],
      target_chains: [
        'NULS2',
        'ETH'
      ],
      contracts: {
        'NULS2': 'NULSd6HgyZkiqLnBzTaeSQfx1TNg2cqbzq51h',
        'ETH': '0xC0134b5B924c2FCA106eFB33C45446c466FBe03e',
        'NEO': '2efdb22c152896964665d0a8214dc7bd59232162'
      },
      targets: {
        'NULS2': 'NULSd6HgUUzDe6HxEB3SoyPR2V1DTvnaBFnVV',
        'ETH': '',
        'NEO': 'AGSx4cCpJbv5b767Yr2xSvuxgYPP8evejL'
      }
    }
  },
  computed: { 
    aleph_balance() {
      return (this.source_balances.ALEPH != undefined) ? this.source_balances.ALEPH : 0
    }
  },
  methods: {
    async login_o3() {
      let account = await neoDapi.getAccount()
      console.log(account)
      this.source_account = {
        'type': 'NEO',
        'address': account.address,
        'label': account.label,
        'source': 'o3'
      }
    },
    async get_source_balances(account) {
      if (account.type === 'NULS2') {
        return await get_nuls_balance_info(
          account.address, 'https://nuls.world'
        )
      } else if (account.type === 'ETH') {
        return await get_ethereum_balance_info(
          account.address, 'https://api.ethplorer.io'
        )
      } else if (account.type === 'NEO') {
        return await get_neo_balance_info(
          account.address, 'https://api.neoscan.io'
        )
      } else {
        return {}
      }
    },
    guess_address_type(address) {
      let address_length = address.length
      if (address.startsWith("0x") && (address_length == 42))
          return "ETH"
      else if (address.startsWith("NULSd6") && (address_length == 37))
          return "NULS2"
      else if (address.startsWith("A") && (address_length == 34))
          return "NEO"
      else if (address.startsWith("tz") && (address_length == 36))
          return "TEZOS"
      else
          return null
    },
    check_address() {
      let address_type = this.guess_address_type(this.target_address)
      if (address_type != null) {
        this.target_chain = address_type
        return false
      }
      return true
    },
    async do_swap() {
      if (this.source_account.type == "NEO") {
        if (this.source_account.source == "o3") {
          console.log("swapping o3")
          let result = await neoDapi.send({
            fromAddress: this.source_account.address,
            toAddress: this.targets.NEO,
            amount: this.amount.toString(),
            asset: this.contracts.NEO,
            remark: this.target_address
          })
          console.log(result)

          if (result.txid) {
            this.out_txid = result.txid
          }
        }
      }
    }
  },
  watch: {
    async source_account(new_account) {
      this.source_balances = await this.get_source_balances(new_account)
    }
  }
}
</script>
<style scoped>
</style>