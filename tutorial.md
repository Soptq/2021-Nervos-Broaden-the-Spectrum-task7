# Porting Tutorial
In this tutorial, I will share how I ported an Ethereum Dapp to Polyjuice. Our goal is to successfully port a [Todo List](https://github.com/AndrewJBateman/blockchain-ethereum-contract) Dapp.

To follow this tutorial, you need to have `node` and `yarn` installed.

## Download the original DApp
Before we start, we need to download the original DApp and install all dependencies it needed.

```
git clone https://github.com/AndrewJBateman/blockchain-ethereum-contract.git
cd blockchain-ethereum-contract
yarn

# In order to port dapp to polyjuice, we need two more libraries.
yarn add @polyjuice-provider/web3
yarn add nervos-godwoken-integration
```

## Porting Dapp to Polyjuice
### `Truffle` Configuration
`Truffle` is a powerful blockchain test-suite that help us test the dapp locally. `Truffle` can also help us compile smart contracts and deploy them  to the blockchain. In order to let `Truffle` know what blockchain we are gonna operate on, we need to slightly edit the `truffle-config.js` file in the project root.

Specifically, we firstly import the Polyjuice provider by injecting some codes to the header of the config file:

```javascript
const PolyjuiceHttpProvider = require('@polyjuice-provider/web3')
const CONFIG = {
    WEB3_PROVIDER_URL: 'http://godwoken-testnet-web3-rpc.ckbapp.dev',
    ROLLUP_TYPE_HASH: '0x4cc2e6526204ae6a2e8fcf12f7ad472f41a1606d5b9624beebd215d780809f6a',
    ETH_ACCOUNT_LOCK_CODE_HASH: '0xdeec13a7b8e100579541384ccaf4b5223733e4a5483c3aec95ddc4c1d5ea5b22'
};
const godwokenRpcUrl = CONFIG.WEB3_PROVIDER_URL;
const providerConfig = {
    rollupTypeHash: CONFIG.ROLLUP_TYPE_HASH,
    ethAccountLockCodeHash: CONFIG.ETH_ACCOUNT_LOCK_CODE_HASH,
    web3Url: godwokenRpcUrl
};
```

And then we add a new network to the `Truffle` by inserting a dictionary object to the `networks` block in `module.exports`:

```javascript
polyjuice: {
	  provider: () => new PolyjuiceHttpProvider.PolyjuiceHttpProvider(godwokenRpcUrl, providerConfig),
	  network_id: 71393,
	  gas: 6000000,
	  from: <YOUR ETH ADDRESS>,
	}
```

Be aware to replace `<YOUR ETH ADDRESS>` to your ethereum wallet address.

### Deploy Contracts

Once the `truffle-config.js` is configured, you can deploy the contracts to the network. Since the contract deployment fee will be charged by your ethereum wallet in `truffle-config.js`, make sure you have some balance in that wallet.

You can deploy the contracts simply by:

```javascript
truffle console --network polyjuice
```

`Truffle` will automatically handle contract compiling and migrating for you, you should be able to see a console log as follows:

```
...
Deploying Migrations...
... 0x123...
```

Remember that address (`0x123` in this example), that will be your contract address, you will need it to call functions later.

### Porting Frontend

After successfully deploying contracts, the only left task we need to do is to port the frontend as well, since there are some logic differences between ethereum and polyjuce network.

In our example, the front end is fairly simple, and we don’t need to modularize components by leveraging `require` or `import`. Instead, we can just convert the `@polyjuice-provider/web3` and `nervos-godwoken-integration` library to a static script, and link it in the html header.

Converted Static Script:
[@polyjuice-provider/web3](https://github.com/Soptq/2021-Nervos-Broaden-the-Spectrum-task7/raw/main/app/client/polyjuice.js)
[nervos-godwoken-integration](https://github.com/Soptq/2021-Nervos-Broaden-the-Spectrum-task7/raw/main/app/client/nervos-godwoken-integration.js)

We can link these 2 script by inserting script tags in `client/index.html`:

```html
<script src="/libs/@truffle/contract/dist/truffle-contract.min.js"></script>
<script src="./nervos-godwoken-integration.js"></script>
```

Then, in the `client/app.js` file, we need to do some editing as well. Since polyjuice uses its own provider, we must tell the dapp how to use it by replace the following codes with the original `loadWeb3()` content.

```javascript
if (web3) {
			const godwokenRpcUrl = "http://godwoken-testnet-web3-rpc.ckbapp.dev"
			const providerConfig = {
    		rollupTypeHash: "0x4cc2e6526204ae6a2e8fcf12f7ad472f41a1606d5b9624beebd215d780809f6a",
    		ethAccountLockCodeHash: "0xdeec13a7b8e100579541384ccaf4b5223733e4a5483c3aec95ddc4c1d5ea5b22",
    		web3Url: godwokenRpcUrl
			};
			App.web3Provider = new PolyjuiceHttpProvider(godwokenRpcUrl, providerConfig);
			web3 = new Web3(App.web3Provider);
		} else {
			console.log("No ethereum browser is installed. Try installing MetaMask");
		}
```

And in `loadContract()` function, we make our dapp aware of the contract context by feeding it a `json` file generated by contracts compiler. Note that contracts can also be deployed in here, when there is no deployed contract on the network.

```javascript
try {
			const contractAddress = "<YOUR CONTRACT ADDRESS, REPLACE THIS IF YOU HAVE ALREADY DEPLOYED THE CONTRACT, LEAVE BLANK IF YOU DONT>"
			const res = await fetch("TasksContract.json");
			const tasksContractJSON = await res.json();
			if (contractAddress) {
				console.log("Using deployed contract")
				App.tasksContract = new web3.eth.Contract(tasksContractJSON.abi, contractAddress);
			} else {
				console.log("Deploying contract")
				App.tasksContract = new web3.eth.Contract(tasksContractJSON.abi);
				await App.tasksContract.deploy({
					data: tasksContractJSON.bytecode,
	        arguments: []
				}).send({
	        from: App.account,
	        gas: 6000000,
	        to: '0x0000000000000000000000000000000000000000',
	    	});
				console.log(App.tasksContract)
			}
		} catch (error) {
			console.error(error);
		}
```

Note that Polyjuice requires the `gas` set to `6000000` and `to` set to `0x0000000000000000000000000000000000000000`. This modification needs to be made for all contract functions, including `taskCounter()`, `createTask(title, description)`, `toggleDone()` and `tasks(i)`:

```javascript
...
const taskCounter = await App.tasksContract.methods.taskCounter().call({
			gas: 6000000,
			from: App.account,
		});
...
...
const task = await App.tasksContract.methods.tasks(i).call({
				gas: 6000000,
				from: App.account,
			});
...
...
const result = await App.tasksContract.methods.createTask(title, description).call({
				gas: 6000000,
				from: App.account,
			});
...
...
App.tasksContract.methods.toggleDone(taskId).call({
			gas: 6000000,
			from: App.account,
		});
...
```

Finally, run the server by:

```
yarn dev
```

## Congratulation!

Now you should have a working dapp that supports Polyjuice network! Try it out!