# 2021-Nervos-Broaden-the-Spectrum-task7

Task7: Port An Existing Ethereum DApp To Polyjuice

Submitter: Soptq

## Screenshots or video of your application running on Godwoken.

![app running](app-running.png?raw=true "app running")

## Link to the GitHub repository with your application which has been ported to Godwoken. This must be a different application than the one covered in this guide.

It is in this repo, under `app` folder.

```
https://github.com/Soptq/2021-Nervos-Broaden-the-Spectrum-task7/tree/main/app
```

## If you deployed any smart contracts as part of this tutorial, please provide the transaction hash of the deployment transaction, the deployed contract address, and the ABI of the deployed smart contract. (Provide all in text format.)

```
deployment transaction:
0xc82d1b65131e6dddd4ec47691f72d51ae00cc867b8d8c6bae56733f884083569

deployed contract address: 
0x7b48a182479126cac52d6a4637b21ce4cc309656

ABI: 
[
    {
      "inputs": [],
      "stateMutability": "nonpayable",
      "type": "constructor"
    },
    {
      "anonymous": false,
      "inputs": [
        {
          "indexed": false,
          "internalType": "uint256",
          "name": "id",
          "type": "uint256"
        },
        {
          "indexed": false,
          "internalType": "string",
          "name": "title",
          "type": "string"
        },
        {
          "indexed": false,
          "internalType": "string",
          "name": "description",
          "type": "string"
        },
        {
          "indexed": false,
          "internalType": "bool",
          "name": "done",
          "type": "bool"
        },
        {
          "indexed": false,
          "internalType": "uint256",
          "name": "createdAt",
          "type": "uint256"
        }
      ],
      "name": "TaskCreated",
      "type": "event"
    },
    {
      "anonymous": false,
      "inputs": [
        {
          "indexed": false,
          "internalType": "uint256",
          "name": "id",
          "type": "uint256"
        },
        {
          "indexed": false,
          "internalType": "bool",
          "name": "done",
          "type": "bool"
        }
      ],
      "name": "TaskToggledDone",
      "type": "event"
    },
    {
      "inputs": [],
      "name": "taskCounter",
      "outputs": [
        {
          "internalType": "uint256",
          "name": "",
          "type": "uint256"
        }
      ],
      "stateMutability": "view",
      "type": "function",
      "constant": true
    },
    {
      "inputs": [
        {
          "internalType": "uint256",
          "name": "",
          "type": "uint256"
        }
      ],
      "name": "tasks",
      "outputs": [
        {
          "internalType": "uint256",
          "name": "id",
          "type": "uint256"
        },
        {
          "internalType": "string",
          "name": "title",
          "type": "string"
        },
        {
          "internalType": "string",
          "name": "description",
          "type": "string"
        },
        {
          "internalType": "bool",
          "name": "done",
          "type": "bool"
        },
        {
          "internalType": "uint256",
          "name": "createdAt",
          "type": "uint256"
        }
      ],
      "stateMutability": "view",
      "type": "function",
      "constant": true
    },
    {
      "inputs": [
        {
          "internalType": "string",
          "name": "_title",
          "type": "string"
        },
        {
          "internalType": "string",
          "name": "_description",
          "type": "string"
        }
      ],
      "name": "createTask",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    },
    {
      "inputs": [
        {
          "internalType": "uint256",
          "name": "_id",
          "type": "uint256"
        }
      ],
      "name": "toggleDone",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    }
  ]

```
