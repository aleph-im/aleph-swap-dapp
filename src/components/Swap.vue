<template>
  <div>
    <q-card label="Swap">
      <q-card-section class="q-pb-none">
        <div class="text-h6">Swap tokens</div>
      </q-card-section>
      <q-card-section horizontal class="justify-between" v-if="!out_txid">
        <q-card-section class="col">
          <q-select v-model="source_chain" :options="source_chains" label="Source chain" />
          <div v-if="['ETH', 'BSC'].includes(source_chain)">
            <div v-if="(source_account==null)||(source_account.type != source_chain)">
              Login with:
              <br />
              <q-btn color="primary" size="sm" @click="login_metamask">
                <img src="../assets/img/metamask.png" height="24px">
              </q-btn>
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
          <q-field label="Amount" stack-label :hint="fees[target_chain] ? `Fee of ${fees[target_chain]} ALEPH applies` : null">
            <template v-slot:control>
              <div class="self-center full-width no-outline" tabindex="0">{{target_amount}}</div>
            </template>
          </q-field>
        </q-card-section>
      </q-card-section>
      <q-card-section class="text-right" v-if="(source_account.address != undefined)&&!check_address()&&!out_txid">
        <q-btn @click="do_approve" color="primary"
               v-if="(source_account.meta === 'ETH') && !enough_allowance"
               class="q-mr-md" :loading="approving">
          Approve
        </q-btn>
        <q-btn @click="do_swap" color="primary" :disabled="!can_swap" :loading="swapping">
          Swap !
        </q-btn>
      </q-card-section>
      <q-card-section class="text-center" v-else-if="(source_chain == 'NULS2')&&!check_address()&&!out_txid">
        To proceed, please send <strong>{{amount}}</strong> ALEPH<br />
        to <strong>{{targets.NULS2}}</strong><br/>
        with remark <strong>{{prepared_target}}</strong>
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
import { ethers } from "ethers";
import {
  get_nuls_balance_info, get_ethereum_balance_info, get_web3_balance_info
} from '../services/balances'

import TxHash from './TxHash'
import { get_erc20_contract, get_swap_contract } from 'src/services/ethereum';

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
      source_allowance: null,
      target_chain: 'ETH',
      target_address: '',
      approving: false,
      swapping: false,
      eth_chain_id: null,
      out_txid: null,
      amount: 0,
      provider: null,
      source_chains: [
        'NULS2',
        'ETH',
        'BSC'
      ],
      target_chains: [
        'NULS2',
        'ETH'
        // 'BSC'
      ],
      contracts: {
        'NULS2': 'NULSd6HgyZkiqLnBzTaeSQfx1TNg2cqbzq51h',
        'ETH': '0x27702a26126e0B3702af63Ee09aC4d1A084EF628',
        'BSC': '0x82D2f8E02Afb160Dd5A480a617692e62de9038C4',
        'NEO': '2efdb22c152896964665d0a8214dc7bd59232162'
      },
      targets: {
        'NULS2': 'NULSd6HgUUzDe6HxEB3SoyPR2V1DTvnaBFnVV',
        'ETH': '0x047f18e7F21Aa714c6a5f4B346318Eb384434A4b',
        'BSC': '0x5594eA3f85272784f66A282FB3D78fe002B92356'
      },
      chain_ids: {
        'ETH': 1,
        'BSC': 56
      },
      fees: {
        'NULS2': 0,
        'ETH': 40,
        'BSC': 5
      }
    }
  },
  computed: { 
    aleph_balance() {
      return (this.source_balances.ALEPH != undefined) ? this.source_balances.ALEPH : 0
    },
    target_amount() {
      if (this.fees[this.target_chain]) {
        return Math.max(this.amount - this.fees[this.target_chain], 0)
      } else {
        return this.amount
      }
    },
    prepared_target() {
      let target = this.target_address
      if ((this.target_chain === 'BSC') ||
          (this.target_chain === 'ETH')) {
        target = `${this.target_address}@eip155:${this.chain_ids[this.target_chain]}`
      } else if (this.target_chain === 'NULS2') {
        target = `${this.target_address}@nuls`
      }
      return target
    },
    rev_chain_ids() {
      let val = {}
      Object.keys(this.chain_ids).forEach(
        (x => { val[this.chain_ids[x]] = x }))
      return val
    },
    enough_allowance() {
      if (this.source_account.meta === 'ETH') {
        if (this.source_allowance < this.target_amount)
          return false
      }
      return true
    },
    can_swap() {
      if (this.target_amount <= 0)
        return false
      if (this.amount > this.source_balances.ALEPH)
        return false
      if (!this.enough_allowance)
        return false

      return true
    }
  },
  methods: {
    async update_eth_account() {
      this.source_chain = this.rev_chain_ids[this.eth_chain_id]
      let signer = this.provider.getSigner()
      this.source_account = {
        'meta': 'ETH',
        'type': this.source_chain,
        'address': await signer.getAddress(),
        'signer': signer,
        'source': 'signer'
      }
    },
    async login_metamask() {
      await window.ethereum.enable()
      this.provider = new ethers.providers.Web3Provider(window.ethereum)
      this.provider.on("network", async (newNetwork, oldNetwork) => {
        console.log(newNetwork, oldNetwork)
        this.eth_chain_id = newNetwork.chainId
        if (Object.values(this.chain_ids).includes(newNetwork.chainId)) {
          await this.update_eth_account()
        }
      });
      this.provider.on('accountsChanged', () => {
        console.log("hu hu")
      })
    },
    async refresh_account() {
      this.source_balances = await this.get_source_balances(this.source_account)
      this.source_allowance = await this.get_source_allowance(this.source_account)
    },
    async get_source_allowance(account) {
      if (account.meta === 'ETH') {
        let contract = await get_erc20_contract(
          this.contracts[account.type], account.signer)
        let allowance = await contract.allowance(
          account.address,
          this.targets[account.type]
        )
        let decimals = await contract.decimals()
        return allowance / (10**decimals)
      }
    },
    async get_source_balances(account) {
      if (account.type === 'NULS2') {
        return await get_nuls_balance_info(
          account.address, 'https://nuls.world'
        )
      } else if (account.type === 'ETH') {
        return {
          ALEPH: await get_web3_balance_info(
            account.address, account.signer,
            this.contracts['ETH']
          )}
      } else if (account.type === 'BSC') {
        return {
          ALEPH: await get_web3_balance_info(
            account.address, account.signer,
            this.contracts['BSC']
          )}
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
        if ((address_type === 'ETH') && (this.target_chain==='BSC'))
          return false

        this.target_chain = address_type
        return false
      }
      return true
    },
    async do_approve() {
      if (this.source_account.meta == "ETH") {
        this.approving = true
        let contract = get_erc20_contract(
          this.contracts[this.source_account.type],
          this.source_account.signer)
        let transaction = await contract.approve(
          this.targets[this.source_account.type],
          ethers.utils.parseUnits('500000000', 18))
        let receipt = await transaction.wait(3)
        await this.refresh_account()
        this.approving = false
      }
    },
    async do_swap() {
      if (this.source_account.meta == 'ETH') {
        this.swapping = true
        let contract = get_swap_contract(
          this.targets[this.source_account.type],
          this.source_account.signer)
        let transaction = await contract.logSendMemo(
          ethers.utils.parseUnits(this.amount.toString(), 18),
          this.prepared_target)
        console.log(transaction)
        let receipt = await transaction.wait(3)
        console.log(receipt)
        await this.refresh_account()
        await this.$emit('sent')
        this.swapping = false
      }
    }
  },
  mounted() {
    if (window.ethereum) {
      window.ethereum.on('accountsChanged', async (accounts) => {
        await this.login_metamask()
      })

      window.ethereum.on('chainChanged', async (chainId) => {
        await this.login_metamask()
      })
    }
  },
  watch: {
    async source_account(new_account) {
      await this.refresh_account()
    }
  }
}
</script>
<style scoped>
</style>