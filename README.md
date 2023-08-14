# 🧙🏻‍♂️ An Introduction to Full Stack dapp Development 💻

This workshop is built around Scaffold-ETH 2 and The Graph. You will learn how to:

1. Setup a development environment for your dapp using Scaffold-ETH 2 and The Graph
2. Update and deploy your smart contract
3. Create and deploy a Subgraph to The Graph
4. Edit your frontend to interact with both your smart contract and Subgraph

&nbsp;

## ✅ Requirements

Before you begin, you need to install the following tools:

- [Node.js](https://nodejs.org/en/download/)
- Yarn ([v1](https://classic.yarnpkg.com/en/docs/install/) or [v2+](https://yarnpkg.com/getting-started/install))
- [Git](https://git-scm.com/downloads)
- [Docker](https://docs.docker.com/get-docker/)

&nbsp;

## 🏗 Scaffold-ETH 2

🧪 An open-source, up-to-date toolkit for building decentralized applications (dapps) on the Ethereum blockchain. It's designed to make it easier for developers to create and deploy smart contracts and build user interfaces that interact with those contracts.

⚙️ Built using NextJS, RainbowKit, Hardhat, Wagmi, and Typescript.

- ✅ **Contract Hot Reload**: Your frontend auto-adapts to your smart contract as you edit it.
- 🔥 **Burner Wallet & Local Faucet**: Quickly test your application with a burner wallet and local faucet.
- 🔐 **Integration with Wallet Providers**: Connect to different wallet providers and interact with the Ethereum network.

To learn more about Scaffold-ETH checkout the [Github repository](https://github.com/scaffold-eth/scaffold-eth-2).

&nbsp;

## 🧑🏼‍🚀 The Graph

[The Graph](https://thegraph.com/) is a protocol for building decentralized applications (dApps) quickly on Ethereum and IPFS using GraphQL.

- 🗃️ **Decentralized Indexing**: The Graph enables open APIs ("subgraphs") to efficiently index and organize blockchain data.
- 🔎 **Efficient Querying**: The protocol uses GraphQL for streamlined querying blockchain data.
- 🙌 **Community Ecosystem**: The Graph fosters collaboration by empowering developers to build, deploy, and share subgraphs!

For detailed instructions and more context, check out the [Getting Started Guide](https://thegraph.com/docs/en/cookbook/quick-start).

&nbsp;

## 🖥️ Setup Scaffold-ETH Subgraph Package

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

&nbsp;

## 🚀 Setup The Graph Integration

Now that we have spun up our blockchain, started our frontend application and deployed our smart contract, we can start setting up our subgraph and utilize The Graph!

&nbsp;

✅ Step 1: Clean up any old data and spin up our docker containers ✅ 

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

&nbsp;

✅ Step 2: Create and ship our Subgraph ✅

Now we can open up a fourth window to finish setting up The Graph. 😅 In this forth window we will create our local subgraph! 

> Note: You will only need to do this once.

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

&nbsp;

✅ Step 3: Test your Subgraph ✅

Go ahead and head over to your subgraph endpoint and take a look!

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

Next up we will dive into a bit more detail on how The Graph works so that as you start adding events to your smart contract you can start indexing and parsing the data you need for your front end application.

&nbsp;

## 🧑🏼‍💻 Adding more events / Subgraph updates 👩🏽‍💻

Now we want to start making some changes to our contract. We will create a new function and a new event for that function.

&nbsp;

✅ Step 1: Add an event to our contract ✅

> Open up YourContract.sol under packages/hardhat/contracts

Add the following new code.

```
    event SendMessage(address _from, address _to, string message);

    function sendMessage(address _to, string calldata message) external {
        emit SendMessage(msg.sender, _to, message);
    }
```

We can save our contract and then deploy those new changes.

```
yarn deploy --reset
```

&nbsp;

✅ Step 2: Test your new function ✅

Navigate over to http://localhost:3000/debug and send vitalik.eth a message. 

&nbsp;

✅ Step 3: Update the GraphQL schema ✅

After you add an event to your smart contract, you will need to first update the GraphQL schema to include the entities you want to store on your Graph node. If you want to catch up on entities here is a good link to the docs for that.

So, using our message example above, if we wanted to create entities for the Message, the Messenger and the Receiver… this is probably a concise example on how we could link that data using our schema.

> The schema file is located in packages/subgraph/src/schema.graphql

```
type Message @entity {
  id: ID!
  _from: Messenger!
  _to: Receiver!
  message: String!
  createdAt: BigInt!
  transactionHash: String!
}

type Messenger @entity {
  id: ID!
  address: Bytes!
  messages: [Message!] @derivedFrom(field: "_from")
  createdAt: BigInt!
  messageCount: BigInt!
}

type Receiver @entity {
  id: ID!
  address: Bytes!
  createdAt: BigInt!
  messageCount: BigInt!
}
```

&nbsp;

✅ Step 4: Update the Subgraph manifest ✅

You will also need to add these entities to the Subgraph YAML configuration and also add the event handlers as well.

> This file is located in packages/subgraph/subgraph.yaml

```
entities:
        - Greeting
        - Sender
        - Message
        - Messenger
        - Receiver
```

```
eventHandlers:
        - event: SendMessage(address,address,string)
          handler: handleMessage
```

If you are following along, next you will need to copy over your new abi and regenerate the code.

```
yarn abi-copy && yarn codegen
```

&nbsp;

✅ Step 5: Update the mapping script ✅

Next you will need to update the mappings for the files we have edited above.

> The file is located under packages/subgraph/src/mapping.ts

```
import {
  YourContract,
  GreetingChange,
  SendMessage,
} from "../generated/YourContract/YourContract";
```

As well as the schema we edited earlier.

```
import {
  Greeting,
  Sender,
  Messenger,
  Receiver,
  Message,
} from "../generated/schema";
```

Lastly, we will need to add the mapping function.

```
export function handleMessage(event: SendMessage): void {
  let messengerString = event.params._from.toHexString();
  let receiverString = event.params._to.toHexString();

  let messenger = Messenger.load(messengerString);

  if (messenger === null) {
    messenger = new Messenger(messengerString);
    messenger.address = event.params._from;
    messenger.createdAt = event.block.timestamp;
    messenger.messageCount = BigInt.fromI32(1);
  } else {
    messenger.messageCount = messenger.messageCount.plus(BigInt.fromI32(1));
  }

  let receiver = Receiver.load(receiverString);

  if (receiver === null) {
    receiver = new Receiver(receiverString);
    receiver.address = event.params._from;
    receiver.createdAt = event.block.timestamp;
    receiver.messageCount = BigInt.fromI32(1);
  } else {
    receiver.messageCount = receiver.messageCount.plus(BigInt.fromI32(1));
  }

  let message = new Message(
    event.transaction.hash.toHex() + "-" + event.logIndex.toString()
  );

  message.message = event.params.message;
  message._from = messengerString;
  message._to = receiverString;
  message.createdAt = event.block.timestamp;
  message.transactionHash = event.transaction.hash.toHex();

  receiver.save();
  messenger.save();
  message.save();
}
```

After that is done, you are almost done… time to ship it!

&nbsp;

✅ Step 6: Ship your updated Subgraph ✅

```
yarn local-ship
```

If you want to test this out on your own instance of Scaffold-ETH, navigate over to the Debug Contracts tab. Here you can draft up a message and fire it off.

&nbsp;

✅ Step 7: Test your newly deployed Subgraph ✅

Next, lets see if our data is in The Graph. Here is an example query that shows us the first message.

```
{
    messages(first: 1, orderBy: createdAt, orderDirection: asc) {
      id
      _from {
        messages(first: 1) {
          message
        }
      }
    }
  }
```

Data is such a beautiful thing huh? 

&nbsp;

## 🌎 Frontend Development! ✍🏼

Ok so we have successfully created a new function, event and updated our Subgraph to index the data. Now lets lean a bit on the frontend. Scaffold-ETH provides a lot of useful components and boilerplate code that makes it easy to build out a UI.

We will create the following:
- An Address component
- A Balance component
- An input field to update our greeting
- A button to send the 'setGreeting' transaction
- An input field for our message reciever
- A message input for that message
- A button to send the 'sendMessage' transaction
- A table to display our messages

Consult the following documentation to learn more about our implementation.
- [Wagmi Hooks](https://wagmi.sh/react/hooks/useAccount)
- [Scaffold-ETH Hooks](https://github.com/scaffold-eth/eth-ui/blob/next/packages/eth-hooks/README.md)
- [Scaffold-ETH Components](https://github.com/scaffold-eth/eth-components)
- [Additional README](https://scaffold-eth-2-docs.vercel.app/hooks/)

&nbsp;

✅ Step 1: Start with a clean slate in your index.ts file ✅

First let's import everything we will need into our index.ts file. You can also clean out all the info in the return.

> this file is located in packages/nextjs/pages

It should look something like this...

```
import type { NextPage } from "next";
import { MetaHeader } from "~~/components/MetaHeader";

import { useAccount } from "wagmi";
import { Address, AddressInput, Balance } from "~~/components/scaffold-eth";
import {
  useAccountBalance,
  useDeployedContractInfo,
  useScaffoldContractRead,
  useScaffoldContractWrite,
} from "~~/hooks/scaffold-eth";
import { useState } from "react";
import { gql } from "@apollo/client";
import { useQuery } from "@apollo/client";

const Home: NextPage = () => {
  return (
    <>
      <MetaHeader />
    </>
  );
};

export default Home;
```

&nbsp;

✅ Step 2: Create a Address and Balance component ✅

Next we will want to add a div and drop in the following...

```
      <div className="flex items-center flex-col flex-grow pt-10">
        <div>
          <Address address={address} />
          <Balance address={address} />
        </div>
      </div>
```

We will use useAccount() from wagmi to get what we need and display it with Address and Balance from Scaffold-ETH components.

Add the following to index.ts just above return.

```
  const { address } = useAccount();
```

&nbsp;

✅ Step 3: Display the current greeting ✅

Next we can create a div to show the greeting.

```
<div className="p-5 font-black text-xl">{greeting}</div>
```

Add the needed variable above your return to get the data from Scaffold-ETH hooks.

```
  const { data: greeting } = useScaffoldContractRead({
    contractName: "YourContract",
    functionName: "greeting",
  });
```

&nbsp;

✅ Step 4: Create an Address and Balance component for your contract ✅

Next we we will get our contracts address and balance.

```
        <div>
          <Address address={yourContract?.address} />
          <Balance address={yourContract?.address} />
        </div>
```

We can fill this data using useDeployedContractInfo() from the hooks.

```
  const { data: yourContract } = useDeployedContractInfo("YourContract");
```

&nbsp;

✅ Step 5: Create a input field and button for our setGreeting function ✅

Now we need an input field and button to update our greeting using setGreeting...

```
        <div className="p-5">
          <input
            value={newGreeting}
            placeholder="Type here"
            className="input"
            onChange={(e) => setNewGreeting(e.target.value)}
          />
        </div>
        <div className="p-5">
          <button className="btn btn-primary" onClick={setGreeting}>
            Set Greeting
          </button>
        </div>
```

For this we also need to use react state to keep track of what we type along with a useScaffoldContractWrite() hook.

```
  const [newGreeting, setNewGreeting] = useState("");

  const { writeAsync: setGreeting } = useScaffoldContractWrite({
    contractName: "YourContract",
    functionName: "setGreeting",
    args: [newGreeting],
  });
```

&nbsp;

✅ Step 6: Create input fields and button for our sendMessage function ✅

Now lets add the message receipient and message field with a button.

```
        <div className="p-5">
        <AddressInput
            value={newReceiver}
            placeholder="Recepient?"
            name={address}
            onChange={setNewReceiver}
          />
        </div>
        <div className="p-5">
          <input
            value={newMessage}
            placeholder="Message"
            className="input"
            onChange={(e) => setNewMessage(e.target.value)}
          />
        </div>
        <div className="p-5">
          <button className="btn btn-primary" onClick={sendMessage}>
            Send Message
          </button>
        </div>
```

We will need two states and also the useScaffoldContractWrite() hook with the needed arguments.

```
  const [newReceiver, setNewReceiver] = useState("");
  const [newMessage, setNewMessage] = useState("");

  const { writeAsync: sendMessage } = useScaffoldContractWrite({
    contractName: "YourContract",
    functionName: "sendMessage",
    args: [newReceiver, newMessage],
  });
```

&nbsp;

✅ Step 7: Display our messages in a table format ✅

Lastly a table to display our messages.

```
        <h1>Messages</h1>
        <table className="min-w-[70%]">
          <thead>
            <tr>
              <th>From</th>
              <th>To</th>
              <th>Message</th>
            </tr>
          </thead>
          <tbody>
            {messages.map((message) => (
              <tr key={message.id}>
                <td>{message._from.id}</td>
                <td>{message._to.id}</td>
                <td>{message.message}</td>
              </tr>
            ))}
          </tbody>
        </table>
```

We should create a const with our GraphQL query outside of the main function.

```
export const GET_MESSAGES = gql`
query MyQuery {
  messages(first: 10, orderDirection: desc, orderBy: createdAt) {
    message
    _to {
      id
    }
    _from {
      id
    }
  }
}
`;
```

And then load the data like so...

```
  const { loading, error, data: messagesData } = useQuery(GET_MESSAGES);

  const messages = messagesData?.messages || [];
```

If you want to see the full complete file you can do so [here](https://gist.github.com/kmjones1979/26ef9633b61b17f237e88eb41bb688de)!

Woohoo! You did it… I guess you are ready for a hackathon eh?

&nbsp;

## ⛵ Ship to a testnet

Next we want to take our smart contract and deploy it to a testnet!

&nbsp;

✅ Step 1: Create a deployer account ✅

```
yarn run generate
```

You should see the following saved to your hardhats environment variable file
```
👛 Generating new Wallet
📄 Private Key saved to packages/hardhat/.env file
```

&nbsp;

✅ Step 2: Fund the account ✅

```
yarn account
```

This should display your public address along with a fancy QR code. And balances?!? Woooowwwww that is slick! <3

```

 ▄▄▄▄▄▄▄ ▄  ▄  ▄▄▄▄▄▄▄ ▄▄▄▄▄▄▄ 
 █ ▄▄▄ █ █▀▀▄ █▀█▄ ▄▄█ █ ▄▄▄ █ 
 █ ███ █ ▄▄██▀▄█ ▀ ███ █ ███ █ 
 █▄▄▄▄▄█ ▄▀▄▀▄▀▄▀▄ █ ▄ █▄▄▄▄▄█ 
 ▄  ▄▄▄▄▄▄▀█▄▀▄█▄▄ ▄█▀▄  ▄ ▄▄▄ 
 ▄▀▀█ █▄██▀▀▀▀▄█▀▀▄█▄██▀▄▄█▀█  
   ▀▄▄ ▄█▄▄▄▀ █▀▄▀▀▄▄█ ██▀█ ▄▀ 
 ▄█▀▀██▄ ▀▄   █ ▀  ▀█ ▀▄▀█▄███ 
 ▄█▀██ ▄▄▄ ▀ ▄ █▄▀▄▄  ██▄▀▄▀▄█ 
 █▄ █▄ ▄█▄█▄▀▀▀ ▄█▄█▀▄ █▀▀▄▄▀▄ 
 ███▄█▀▄   █ ▀▄█▀ █████▄▄█▀█▄▄ 
 ▄▄▄▄▄▄▄ █▄ █▄ ██▀ █ █ ▄ █ ██  
 █ ▄▄▄ █ █ ▀██▄██▀▀▄ █▄▄▄██▄▄  
 █ ███ █ ▀▄▀▄ ▀▄▄ ▄█▀▀ ▄▄▄██▄▄ 
 █▄▄▄▄▄█ ▄█ ▄█  ▄▀▄█ ▄▀ ▄▄▄▀ ▀ 
▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀

Public address: 0x87f00B2c39F97CD00BC6d09777BC4327aCA39180 

-- localhost -- 📡
   balance: 0
   nonce: 0

```

&nbsp;

✅ Step 3: Deploy! ✅

Now we can deploy to our testnet of choice... in this example we will deploy to sepolia.

```
yarn deploy --network sepolia
```

If all is well you should see the followng success output.

```
deploying "YourContract" (tx: 0xf404021d736271a7a0a84d124ed35250c533efe37c1775367b6510a8836bf0bd)...: deployed at 0x541D469C06990B7F0bd5103b57997cE8a39C050c with 605281 gas
📝 Updated TypeScript contract definition file on ../nextjs/generated/deployedContracts.ts
```

&nbsp;

✅ Step 4: Verification ✅

```
yarn verify --network sepolia
```

You should see the following successful output...

```
verifying YourContract (0x541D469C06990B7F0bd5103b57997cE8a39C050c) ...
waiting for result...
 => contract YourContract is now verified
```
 
 You can also check your contract was successfully on etherscan. [Here](https://sepolia.etherscan.io/address/0x541D469C06990B7F0bd5103b57997cE8a39C050c#code) is the one I deployed and verified.

&nbsp;

## Migrating you Subgraph to the Studio

Now that our smart contract is on a public testnet it is time to push our Subgraph to the Studio.

&nbsp;

✅ Step 1: Create your Subgraph on the Studio ✅

- Navigate to https://thegraph.com/studio
- Connect your wallet
- Click Create a Subgraph
- Name your Subgraph
- Select the test network you deployed to in previous steps

&nbsp;

✅ Step 2: Install the Graph CLI ✅

You can install the Graph CLI globally using the following command.

```
yarn global add @graphprotocol/graph-cli
```

&nbsp;

✅ Step 3: Init your Subgraph ✅

This can be done in a separate folder of your choosing, since it will initate a yarn package. You will need to fill in the required configuration during the initialization process.The Start Block - Can be found on Etherescan if needed so you don't have to index the entire previous blocks.

```
graph init --studio name_of_your_subgraph
```

It should looks something like this...

```
✔ Protocol · ethereum
✔ Subgraph slug · sendmessage
✔ Directory to create the subgraph in · sendmessage
✔ Ethereum network · sepolia
✔ Contract address · 0x541D469C06990B7F0bd5103b57997cE8a39C050c
✔ Fetching ABI from Etherscan
✖ Failed to fetch Start Block: Failed to fetch contract creation transaction hash
  
✔ Start Block · 4089059
✔ Contract Name · Contract
✔ Index contract events as entities (Y/n) · true
  Generate subgraph
  Write subgraph to directory
✔ Create subgraph scaffold
✔ Initialize networks config
✔ Initialize subgraph repository
✔ Install dependencies with yarn
✔ Generate ABI and schema types with yarn codegen
Add another contract? (y/n): 
Subgraph sendmessage created in sendmessage
```

&nbsp;

✅ Step 4: Authenticate to Studio ✅

Grab your authentication string from Auth & Deploy on Subgraph Studio.

```
graph auth --studio auth_key_here
```

Success looks like this:

```
Deploy key set for https://api.studio.thegraph.com/deploy/
```

&nbsp;

✅ Step 5: Run codegen and build your subgraph ✅

You will need to change into the directory where the subgraph was created in the previous step.

```
cd sendmessage
graph codegen && graph build
```

Success will look something like the following!

```
✔ Apply migrations
✔ Load subgraph from subgraph.yaml
  Load contract ABI from abis/Contract.json
✔ Load contract ABIs
  Generate types for contract ABI: Contract (abis/Contract.json)
  Write types to generated/Contract/Contract.ts
✔ Generate types for contract ABIs
✔ Generate types for data source templates
✔ Load data source template ABIs
✔ Generate types for data source template ABIs
✔ Load GraphQL schema from schema.graphql
  Write types to generated/schema.ts
✔ Generate types for GraphQL schema

Types generated successfully

  Skip migration: Bump mapping apiVersion from 0.0.1 to 0.0.2
  Skip migration: Bump mapping apiVersion from 0.0.2 to 0.0.3
  Skip migration: Bump mapping apiVersion from 0.0.3 to 0.0.4
  Skip migration: Bump mapping apiVersion from 0.0.4 to 0.0.5
  Skip migration: Bump mapping apiVersion from 0.0.5 to 0.0.6
  Skip migration: Bump manifest specVersion from 0.0.1 to 0.0.2
  Skip migration: Bump manifest specVersion from 0.0.2 to 0.0.4
✔ Apply migrations
✔ Load subgraph from subgraph.yaml
  Compile data source: Contract => build/Contract/Contract.wasm
✔ Compile subgraph
  Copy schema file build/schema.graphql
  Write subgraph file build/Contract/abis/Contract.json
  Write subgraph manifest build/subgraph.yaml
✔ Write compiled subgraph to build/

Build completed: build/subgraph.yaml

```

&nbsp;

✅ Step 6: Deploy ✅

Now we are ready to deploy to the Studio

```
graph deploy --studio name_of_your_subgraph
```

Choose a version and fire away!

```
Which version label to use? (e.g. "v0.0.1"): 0.0.1
  Skip migration: Bump mapping apiVersion from 0.0.1 to 0.0.2
  Skip migration: Bump mapping apiVersion from 0.0.2 to 0.0.3
  Skip migration: Bump mapping apiVersion from 0.0.3 to 0.0.4
  Skip migration: Bump mapping apiVersion from 0.0.4 to 0.0.5
  Skip migration: Bump mapping apiVersion from 0.0.5 to 0.0.6
  Skip migration: Bump manifest specVersion from 0.0.1 to 0.0.2
  Skip migration: Bump manifest specVersion from 0.0.2 to 0.0.4
✔ Apply migrations
✔ Load subgraph from subgraph.yaml
  Compile data source: Contract => build/Contract/Contract.wasm
✔ Compile subgraph
  Copy schema file build/schema.graphql
  Write subgraph file build/Contract/abis/Contract.json
  Write subgraph manifest build/subgraph.yaml
✔ Write compiled subgraph to build/
  Add file to IPFS build/schema.graphql
                .. QmTLSV6vUwnPYyi9oqMJ3Ds3TkgE1A8PEaYa5yhbd3y73b
  Add file to IPFS build/Contract/abis/Contract.json
                .. QmT5j3kGMkVjUVaW8MhMKRSnZXdTDrTSUcf5MC9hFKNHYf
  Add file to IPFS build/Contract/Contract.wasm
                .. QmeuaTgxLJKp8N2R2RCiQFvJa1fPz81tytc4xECBoRCpjv
✔ Upload subgraph to IPFS

Build completed: QmUqgKBRWxFGNG6oPZuZxuCwJbEvKe6UbKCe8WTcDJvusk
```

&nbsp;

✅ Step 7: Send a transaction and verify in Subgraph Playground ✅

On Etherscan you can send a transaction directly to your contract on the Contract -> Write Contract tab.

Our Query:

```
{
  sendMessages(first: 5) {
    id
    _from
    _to
    message
  }
}
```

Our data object response: 

```
{
  "data": {
    "sendMessages": [
      {
        "id": "0x053e32f85f9f485334119585abfc73e507a4ce86e968130b90410df70eb3a66e71000000",
        "_from": "0x142cd5d7ac1ea8919f1644af1870792b9f77d44a",
        "_to": "0x007e483cf6df009db5ec571270b454764d954d95",
        "message": "I love you"
      }
    ]
  }
}
```



&nbsp;

## Updating and deploying our Frontend

Now that we have our contract deployed to a testnet and the data is getting indexed in Subgraph Studio. It is time to update our frontend.

&nbsp;

✅ Step 1: Update the Scaffold-ETH config ✅

Update the configuration to point to the testnet that you deployed to in previous steps.

> The scaffold.config.ts is located in packages/nextjs

```
  targetNetwork: chains.sepolia,
```

&nbsp;

✅ Step 2: Update our GraphQL URL to point to our development endpoint ✅

The development endpoint for your subgraph can be found on the details tab in Subgraph Studio.

> Edit _app.tsx located in packages/nextjs/pages

```
  const subgraphUri = "https://api.studio.thegraph.com/query/51078/sendmessage-test/version/latest";
  const apolloClient = new ApolloClient({
    uri: subgraphUri,
    cache: new InMemoryCache(),
  });
```

&nbsp;

✅ Step 3: Fix our query and table ✅

We generated a completely new schema and Subgraph configuration when we used the CLI so we should go in and update both to fix the data query and table configuration.

Firt, update the query to grab sendMessages as the data object.

```
  const messages = messagesData?.sendMessages || []; 

```

Update our query syntax to match the new schema.

```
export const GET_MESSAGES = gql`
{
  sendMessages(first: 5) {
    id
    _from
    _to
    message
  }
}
`;
```

Then we can update the table as well.

```
        <h1>Messages</h1>
        <table className="min-w-[70%]">
          <thead>
            <tr>
              <th>From</th>
              <th>To</th>
              <th>Message</th>
            </tr>
          </thead>
          <tbody>
            {messages.map((message) => (
              <tr key={message.id}>
                <td>{message._from}</td>
                <td>{message._to}</td>
                <td>{message.message}</td>
              </tr>
            ))}
          </tbody>
        </table>
```

&nbsp;

✅ Step 4: YOLO Vercel! ✅

Last step is to push our frontend to vercel! This is easy with the following command, which essentiall skips build errors with the following command line switches 'vercel --build-env NEXT_PUBLIC_IGNORE_BUILD_ERROR=true'

This might take some time, so grab a coffee. ☕

```
yarn vercel:yolo
```

```
Vercel CLI 28.20.0
? Set up and deploy “~/projects/ethereum/scaffold-eth-2-subgraph-package-workshop/packages/nextjs”? [Y/n] 
y
? Which scope do you want to deploy to? myscope
? Link to existing project? [y/N] n
? What’s your project’s name? sendMessage
? In which directory is your code located? ./
Local settings detected in vercel.json:
Auto-detected Project Settings (Next.js):
- Build Command: next build
- Development Command: next dev --port $PORT
- Install Command: `yarn install`, `pnpm install`, or `npm install`
- Output Directory: Next.js default
? Want to modify these settings? [y/N] n
🔗  Linked to kevin-kevinjonescr/testing (created .vercel)
🔍  Inspect: https://vercel.com/kevin-kevinjonescr/testing/E2rfnyzC4ud5DskrwhybQ4Hiicjx [2s]
✅  Production: https://testing-red.vercel.app [3m]
📝  Deployed to production. Run `vercel --prod` to overwrite later (https://vercel.link/2F).
💡  To change the domain or build command, go to https://vercel.com/kevin-kevinjonescr/testing/settings
╭──────────────────────────────────────────────────────────╮
│                                                          │
│           Update available! v28.20.0 ≫ v31.2.3           │
│   Changelog: https://github.com/vercel/vercel/releases   │
│         Run `yarn add vercel@latest` to update.          │
│                                                          │
╰──────────────────────────────────────────────────────────╯

```

&nbsp;

## 💁🏻 Resources / More help!
- [Wagmi Hooks](https://wagmi.sh/react/hooks/useAccount)
- [Scaffold-ETH Hooks](https://github.com/scaffold-eth/eth-ui/blob/next/packages/eth-hooks/README.md)
- [Scaffold-ETH Components](https://github.com/scaffold-eth/eth-components)
- [Additional README](https://scaffold-eth-2-docs.vercel.app/hooks/)
- [Kevin's Social Media Contacts](https://hihello.me/p/6a93d967-1d9f-4818-ae0c-2dc9f86e01aa)
- [Kevin's Mirror.xyz post](https://mirror.xyz/cryptomastery.eth/uGHEHnskoVwX-mWjAiidXfGt6QowCoKl_yX4okwZc0E)
- [Scaffold-ETH Stuff](https://hihello.me/p/b914b816-fb27-4909-9525-16c74c7e7eef)
- [Scaffold-ETH Workshops at ETH Global](https://www.youtube.com/results?search_query=Scaffold-ETH+Workshop+ETHGlobal+Kevin+Jones)
- [The Graph Workshops at ETH Global](https://www.youtube.com/results?search_query=The+Graph+Workshop+ETHGlobal)
- [0 to Buidl Guidl](https://lulox.notion.site/0-to-BuidlGuidl-4e1e835ba37c414199fe7a63cb5807e3)
- [Austin's Web2 to Web3 Curriculum](https://github.com/austintgriffith/web2-to-web3-curriculum)
- [Naders Web3 Resources for Developers](https://naderdabit.notion.site/Nader-s-web3-Resources-for-Developers-a200ed2ef21c4d578dc158df2b882c63)
- [Eda's Developer Resources](https://github.com/edakturk14/ethereum-developer-resources)