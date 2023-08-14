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


# Setup Scaffold-ETH Subgraph Package

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


### Adding more events to your contract and modifications to your Subgraph

Now we want to start making some changes to our contract. We will create a new function and a new event for that function.

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

Navigate over to http://localhost:3000/debug and send vitalik.eth a message.

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

After that is done, you are almost done… Simple ship it!

```
yarn local-ship
```

If you want to test this out on your own instance of Scaffold-ETH, navigate over to the Debug Contracts tab. Here you can draft up a message and fire it off.

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

Woohoo! You did it… I guess you are ready for a hackathon eh?


### Frontend!!!

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

Lastly a table to display our messages.

```
        <h1>Messages</h1>
        <table className="min-w-full">
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