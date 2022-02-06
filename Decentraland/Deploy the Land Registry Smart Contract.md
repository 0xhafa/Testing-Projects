# Deploy the Land Registry Smart Contract
Decentraland Land Smart Contracts: https://github.com/decentraland/land
To Understand how to interact with the contract: https://github.com/decentraland/proposals/blob/master/dsp/dsp-0010/0010.md

## Overview
The LAND contract keeps a record of all the land parcels, who their owner is, and what data is associated with them. The data associated can be an IPFS identifier, an IPNS url, or a simple HTTPS endpoint with a land description file.

### Prerequisites:
1. Install metamask or another waller provider and get ETH  on Ropsten tesnet. https://faucet.metamask.io/
2. Get a Ethereum Node Provider key: https://www.alchemy.com/ https://infura.io/ 

## Deploying the contracts
First, clone the land contracts:
```git
git clone https://github.com/decentraland/land.git
``` 

Then, install all dependencies:
```shell
cd land
npm install
```

Then, configure the enviroment:
```shell
touch .env
``` 

Add your provider key and wallet private key in the .env file.

then compile the contracts:
```
npx hardhat compile
```

Then, inside the deploy folder at a file called `deploy.js`.

In the file add and save:
```js
const hre = require('hardhat');

async function main() {
  const Contract = await hre.ethers.getContractFactory('contracts/land/LANDRegistry.sol:LANDRegistry');
  const contract = await (await Contract.deploy()).deployed();

  console.log('LANDRegistry contract deployed to:', contract.address);
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

Now, deploy the contract to the ropsten tesnet:
```shell
npx hardhat run scripts/deploy.js --network ropsten
```
