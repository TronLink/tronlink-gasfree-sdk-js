# ⚠️ This Repository Has Been Migrated

This project is **no longer maintained** in this repository.

> ✅ It has been **migrated to a new repository maintained by the gasfree.io developer community**:  
👉 [https://github.com/gasfreeio/gasfree-sdk-js](https://github.com/gasfreeio/gasfree-sdk-js)

All future updates, issues, and contributions should be directed to the new repository.

---
# tronlink-gasfree-sdk-js
tronlink-gasfree-sdk-js is a toolkit developed by TronLink based on the GasFree API specification. It facilitates the integration of the non-gas TRC20 token transfer service for the web & browser extension platform.

This SDK is provided by TronLink, while the definition & maintenance of the APIs are managed by the official GasFree project. For more information, visit [https://gasfree.io](https://gasfree.io).

Key Features:
 - Generate GasFree Addresses from User Addresses
 - Generate GasFree Transfer Message Hash


## Demo
 - [tronGenerateGasFreeAddress](demo/tronGenerateGasFreeAddress.ts)
 - [tronGetGasFreeTransactionJson](demo/tronGetGasFreeTransactionJson.ts)
 - [tronGetGasFreeLedgerRawHash](demo/tronGetGasFreeLedgerRawHash.ts)


## Usage

#### quick start
initialization:
```javascript
const tronGasFree = new TronGasFree({
	chainId: Number('0x2b6653dc'), // use mainnet default config
});
```

generate gas free address:
```javascript
import { TronGasFree } from '@tronlink/gasfree-sdk-js'

try {
	const userAddress = 'your tron wallet address'
	const tronGasFree = new TronGasFree({
		chainId: Number('0x2b6653dc'), // use mainnet default config
	});
	const tronGasFreeAddress = tronGasFree.generateGasFreeAddress(userAddress);
} catch (error) {
	console.log(error);
}
```

get standard TIP712 gas free transaction json object:
```typescript

try {
	const tronGasFree = new TronGasFree({
		chainId: Number('0x2b6653dc'), // use mainnet default config
	});
	const transactionJson = tronGasFree.assembleGasFreeTransactionJson({
		token: 'transaction token contract address',
		serviceProvider: 'gas free transaction service provider',
		user: 'your tron wallet address, NOT your gas free address',
		receiver: 'tron receiver address, it can be a wallet address or a gasfree contract address',
		value: 'transfer value, decimal string number',
		maxFee: 'max transfer gas fee, decimal string number',
		deadline: 'timestamp, transaction deadline',
		version: "version of the signature algorithm used by gasfree offline transaction, the current version is 1",
		nonce: 'gas free transaction nonce, decimal string number',
	});
} catch (error) {
}
```

#### initialization
 use Tron mainnet default config:
```typescript
const tronGasFree = new TronGasFree({
	chainId: Number('0x2b6653dc'),
});
```

if you want to use testnet config:
```typescript
const tronGasFree = new TronGasFree({
	chainId: Number('0xcd8690dc'), // nile
});
```
or
```typescript
const tronGasFree = new TronGasFree({
	chainId: Number('0x94a9059e'), // shasta
});
```

If you have your own GasFreeController contract, or your own chain:
```typescript
const tronGasFree = new TronGasFree({
	chainId: 0, // your chainId, number
	gasFreeController: 'your gas free controller address contract, should deployed on the chain in advance',
	beacon: 'your beacon',
	creationCode: 'your GasFreeController creation code',
});
```
#### generate GasFree address
```javascript
import { TronGasFree } from '@tronlink/gasfree-sdk-js'

try {
	const userAddress = 'your tron wallet address'
	const tronGasFree = new TronGasFree({
		chainId: Number('0x2b6653dc'), // use mainnet default config
	});
	const tronGasFreeAddress = tronGasFree.generateGasFreeAddress(userAddress);
} catch (error) {
	console.log(error);
}
```

different chains and different GasFreeControllers will result in inconsistent GasFree addresses.
```javascript
const userAddress = 'your tron wallet address'
const tronGasFree = new TronGasFree({
	chainId: Number('0xcd8690dc'), // nile
});
const tronGasFreeAddress2 = tronGasFree.generateGasFreeAddress(userAddress);
// tronGasFreeAddress2 is different from tronGasFreeAddress
```


#### Get standard TIP712 gas free transaction json object
```typescript
try {
	const tronGasFree = new TronGasFree({
		chainId: Number('0x2b6653dc'), // use mainnet default config
	});
	const transactionJson = tronGasFree.assembleGasFreeTransactionJson({
		token: 'transaction token contract address',
		serviceProvider: 'gas free transaction service provider',
		user: 'your tron wallet address, NOT your gas free address',
		receiver: 'tron receiver address, it can be a wallet address or a gasfree contract address',
		value: 'transfer value, decimal string number',
		maxFee: 'max transfer gas fee, decimal string number',
		deadline: 'timestamp, transaction deadline',
		version: "version of the signature algorithm used by gasfree offline transaction, the current version is 1",
		nonce: 'gas free transaction nonce, decimal string number',
	});
} catch (error) {
}
```

#### normal GasFree signature
```typescript
import TronWeb from 'tronweb';
import { TronGasFree } from '@tronlink/gasfree-sdk-js';

const PRIVATE_KEY = 'your private key';

try {
	const tronGasFree = new TronGasFree({
		chainId: Number('0x2b6653dc'), // use mainnet default config
	});

	const { domain, types, message } = tronGasFree.assembleGasFreeTransactionJson({
		token: 'transaction token contract address',
		serviceProvider: 'gas free transaction service provider',
		user: 'your tron wallet address, NOT your gas free address',
		receiver: 'tron receiver address, it can be a wallet address or a gasfree contract address',
		value: 'transfer value, decimal string number',
		maxFee: 'max transfer gas fee, decimal string number',
		deadline: 'timestamp, transaction deadline',
		version: "version of the signature algorithm used by gasfree offline transaction, the current version is 1",
		nonce: 'gas free transaction nonce, decimal string number',
	});

  const res = await TronWeb.Trx._signTypedData(domain, types, message, PRIVATE_KEY);
} catch (error) {
}
```

#### ledger GasFree signature
```typescript
import { TronGasFree } from '@tronlink/gasfree-sdk-js'
import AppTrx from '@ledgerhq/hw-app-trx';
import TransportWebHID from '@ledgerhq/hw-transport-webhid';

try {
	const tronGasFree = new TronGasFree({
		chainId: Number('0x2b6653dc'), // use mainnet default config
	});
	const { permitTransferMessageHash } =
		tronGasFree.getGasFreeLedgerRawHash({
			message: {
				token: 'transaction token contract address',
				serviceProvider: 'gas free transaction service provider',
				user: 'your tron wallet address, NOT your gas free address',
				receiver: 'tron receiver address, it can be a wallet address or a gasfree contract address',
				value: 'transfer value, decimal string number',
				maxFee: 'max transfer gas fee, decimal string number',
				deadline: 'timestamp, transaction deadline',
				version: "version of the signature algorithm used by gasfree offline transaction, the current version is 1",
				nonce: 'gas free transaction nonce, decimal string number',
			},
		});
	const transport = await TransportWebHID.create();
	const app = new AppTrx(transport);
	const path = 'you ledger address path';
	const res = await app.signTransactionHash(path, permitTransferMessageHash);
} catch (error) {
}
```


## Running the tests

The project includes a suite of tests for the Tron GasFree sdk. To run these tests, use the
following command:

```bash
pnpm test
```

## Local build

These instructions will get you a copy of the project up and running on your local machine for
development and testing purposes.

### Prerequisites

- Node.js >= 18.19.0
- pnpm >= 7.32.0
- TypeScript >= 4.9.5

### Build

##### 1. Install pnpm

This project recommends using `pnpm` as the build tool Make sure node is installed:

Make sure the node version meets the requirements:
```bash
node -v
```

and install `pnpm`:
```bash
npm i -g pnpm
```

##### 2. Clone the repository

You can use `git clone` to download or download the code directly

##### 3. Install dependencies

```bash
pnpm install
```

##### 4. Compile TypeScript files

```bash
pnpm build
```

### Built With

- [TypeScript](https://www.typescriptlang.org/)
- [Node.js](https://nodejs.org/)

## Integrity Check

The package files will be signed using a GPG key pair, and the correctness of the signature will be verified using the following public key:

```
pub: 7B910EA80207596075E6D7BA5D34F7A6550473BA
uid: build_tronlink <build@tronlink.org>
```


## License

This project is licensed under the Apache License Version 2.0 - see the [LICENSE](LICENSE) file for
details
