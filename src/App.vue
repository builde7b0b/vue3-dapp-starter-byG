<script setup lang="ts">
import LayoutHeader from './components/LayoutHeader.vue'
import { MetaMaskConnector, WalletConnectConnector, CoinbaseWalletConnector, SafeConnector, Connector } from 'vue-dapp'
import { ref, onBeforeMount } from 'vue'
import Web3 from 'web3'
import MyContractABI from './ABI.json'
import Home from './views/Home.vue'

const isDev = import.meta.env.DEV
    const infuraId = isDev ? import.meta.env.VITE_INFURA_KEY : 'c66d5493eff848ca89349923e7d1131a'

let connectors: Connector[] = [
	new MetaMaskConnector({
		appUrl: 'http://localhost:3000',
	}),
	new WalletConnectConnector({
		qrcode: true,
		rpc: {
			1: `https://mainnet.infura.io/v3/${infuraId}`,
			4: `https://rinkeby.infura.io/v3/${infuraId}`,
		},
	}),
	new CoinbaseWalletConnector({
		appName: 'Vue Dapp',
		jsonRpcUrl: `https://mainnet.infura.io/v3/${infuraId}`,
	}),
]

const connectorsCreated = ref(false)

onBeforeMount(async () => {
	const safe = new SafeConnector()

	try {
		if (await safe.isSafeApp()) {
			connectors = [safe]
		}
	} catch (err: any) {
		console.error(err)
	}

	connectorsCreated.value = true
})

const autoConnectErrorHandler = (err: any) => {
	console.error(err)
}

const connectErrorHandler = (err: any) => {
	console.error(err)
	}

   



</script>

<template>
	<layout-header />
	<Suspense>
		<template #default>
			<router-view></router-view>
		</template>
		<template #fallback>
			<div>Loading...</div>
		</template>
	</Suspense>
	
	<vd-board v-if="connectorsCreated"
			  :connectors="connectors"
			  dark
			  :autoConnectErrorHandler="autoConnectErrorHandler"
			  :connectErrorHandler="connectErrorHandler" />

	<div>
		<Home />
	</div>
</template>
