# An Introduction to Full Stack dapp Development

This workshop is built around Scaffold-ETH 2 and The Graph.

## Requirements

Before you begin, you need to install the following tools:

- [Node.js](https://nodejs.org/en/download/)
- Yarn ([v1](https://classic.yarnpkg.com/en/docs/install/) or [v2+](https://yarnpkg.com/getting-started/install))
- [Git](https://git-scm.com/downloads)
- [Docker](https://docs.docker.com/get-docker/)

## 🏗 Scaffold-ETH 2

🧪 An open-source, up-to-date toolkit for building decentralized applications (dapps) on the Ethereum blockchain. It's designed to make it easier for developers to create and deploy smart contracts and build user interfaces that interact with those contracts.

⚙️ Built using NextJS, RainbowKit, Hardhat, Wagmi, and Typescript.

- ✅ **Contract Hot Reload**: Your frontend auto-adapts to your smart contract as you edit it.
- 🔥 **Burner Wallet & Local Faucet**: Quickly test your application with a burner wallet and local faucet.
- 🔐 **Integration with Wallet Providers**: Connect to different wallet providers and interact with the Ethereum network.


### Setup Scaffold-ETH Subgraph Package

First, we will start out with a special build of Scaffold-ETH 2 written by Simon from Edge and Node… Thanks Simon! 🫡

```
git clone -b subgraph-package \
  https://github.com/scaffold-eth/scaffold-eth-2.git \
  scaffold-eth-2-subgraph-package
```

Once you have this checked out on your machine, navigate into the directory and install all of the dependencies using yarn.

```
cd scaffold-eth-2-subgraph-package && \
  yarn install
```

Next, we will want to start up our local blockchain so that we can eventually deploy and test our smart contracts. Scaffold-ETH 2 comes with Hardhat by default. To spin up the chain just type the following yarn command…

```
yarn chain
```

> You will keep this window up and available so that you can see any output from hardhat console. 🖥️

Next we are going to spin up our frontend application. Scaffold-ETH 2 comes with NextJS by default and also can be started with a simple yarn command. You will need to open up a new command line and type the following…

```
yarn start
```

> You will also want to keep this window up at all times so that you can debug any code changes you make to NextJS, debug performance or just check that the server is running properly.

Next, you will want to open up a third window where you can deploy your smart contract, along with some other useful commands found in Scaffold-ETH. To do a deploy you can simply run the following…

```
yarn deploy
```

> You should get a tx along with an address and amount of gas spent on the deploy. ⛽

If you navigate to http://localhost:3000 you should see the NextJS application. Explore the menus and features of Scaffold-ETH 2! Someone call in an emergency, cause hot damn that is fire! 🔥

### Setup The Graph Integration

> Now that we have spun up our blockchain, started our frontend application and deployed our smart contract, we can start setting up our subgraph and utilize The Graph!

First run the following to clean up any old data. Do this if you need to reset everything.

```
yarn clean-node
```

> We can now spin up a graph node by running the following command… 🧑‍🚀

```
yarn run-node
```

This will spin up all the containers for The Graph using docker-compose. You will want to keep this window open at all times so that you can see log output from Docker.

> As stated before, be sure to keep this window open so that you can see any log output from Docker. 🔎

> Now we can open up a fourth window to finish setting up The Graph. 😅

In this forth window we will create our local subgraph! You will only need to do this once.

```
yarn local-create
```

> You should see some output stating your Subgraph has been created along with a log output on your graph-node inside docker.

Next we will ship our subgraph! You will need to give your subgraph a version after executing this command. (e.g. 0.0.1).

```
yarn local-ship
```

> This command does the following all in one… 🚀🚀🚀

- Copies the contracts ABI from the hardhat/deployments folder
- Generates the networks.json file
- Generates AssemblyScript types from the subgraph schema and the contract ABIs.
- Compiles and checks the mapping functions.
- … and deploy a local subgraph!

> If you get an error ts-node you can install it with the following command

```
npm install -g ts-node
```

You should get a build completed output along with the address of your Subgraph endpoint.

```
Build completed: QmYdGWsVSUYTd1dJnqn84kJkDggc2GD9RZWK5xLVEMB9iP

Deployed to http://localhost:8000/subgraphs/name/scaffold-eth/your-contract/graphql

Subgraph endpoints:
Queries (HTTP):     http://localhost:8000/subgraphs/name/scaffold-eth/your-contract
```

> Go ahead and head over to your subgraph endpoint and take a look!

> Here is an example query…

```
  {
    greetings(first: 25, orderBy: createdAt, orderDirection: desc) {
      id
      greeting
      premium
      value
      createdAt
      sender {
        address
        greetingCount
      }
    }
  }
```

> If all is well and you’ve sent a transaction to your smart contract then you will see a similar data output!

> Next up we will dive into a bit more detail on how The Graph works so that as you start adding events to your smart contract you can start indexing and parsing the data you need for your front end application.


### Resources / More help!

- [Example UI](https://gist.github.com/kmjones1979/26ef9633b61b17f237e88eb41bb688de)
- [Wagmi Hooks](https://wagmi.sh/react/hooks/useAccount)
- [Kevin's Social Media Contacts](https://hihello.me/p/6a93d967-1d9f-4818-ae0c-2dc9f86e01aa)
- [Scaffold-ETH Stuff](https://hihello.me/p/b914b816-fb27-4909-9525-16c74c7e7eef)
- [Scaffold-ETH Workshops at ETH Global](https://www.youtube.com/results?search_query=Scaffold-ETH+Workshop+ETHGlobal+Kevin+Jones)
- [The Graph Workshops at ETH Global](https://www.youtube.com/results?search_query=The+Graph+Workshop+ETHGlobal)
- [0 to Buidl Guidl](https://lulox.notion.site/0-to-BuidlGuidl-4e1e835ba37c414199fe7a63cb5807e3)
- [Austin's Web2 to Web3 Curriculum](https://github.com/austintgriffith/web2-to-web3-curriculum)
- [Naders Web3 Resources for Developers](https://naderdabit.notion.site/Nader-s-web3-Resources-for-Developers-a200ed2ef21c4d578dc158df2b882c63)
- [Eda's Developer Resources](https://github.com/edakturk14/ethereum-developer-resources)