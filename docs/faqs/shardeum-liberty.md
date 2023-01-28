---
title: Shardeum Liberty
sidebar_position: 4
---

## What is Shardeum?

Shardeum is an EVM-based sharded layer 1 blockchain that uses dynamic state sharding to increase transactions per second (TPS) with the addition of every new node. This will allow the network to have very low and constant transaction fees forever asides from infinite scalability.

## Which languages are used for smart contracts on Shardeum?

EVM-based languages are used to compile smart contracts; this includes Solidity and Vyper.

## How can I contribute to Liberty as a developer?

You can build and deploy smart contracts on Liberty by following this developer documentation. Further, list your dApps you are building on Liberty [here](https://forms.gle/BFLXtLn9Urq9KH998) to publicize it among the community and become a leader in the Shardeum ecosystem.

Pro-tip: If you already have a smart contract deployed on Ethereum, you can migrate it to Liberty within a few minutes.

## What can the community do on Liberty?

Everyone in the Shardeum community can add Liberty network to their wallet, request testnet SHM from the faucet, and interact with the [smart contracts built on it](https://bit.ly/liberty-dapps). You may even qualify for future rewards/airdrops such projects would offer.

## I already have a smart contract deployed on Ethereum. Can I easily migrate it to Liberty?

Since Shardeum is EVM-based, it's as simple as deploying your existing code to the Shardeum network. If you have built for Ethereum, you have built for Shardeum!

## What rewards can I expect if I build dApps on Shardeum Liberty?

There will be an ecosystem fund setup to reward devs for creating innovative apps on Shardeum. You would be able to apply for a grant. More information will be posted on the Shardeum.org site about the process to apply.

## I found some bugs on Shardeum Explorer. How do we report it?

Please use this form to report bugs when you face any issues on Liberty so our developers can look into it for improvement opportunities: https://forms.gle/LzPB7aoHQ6sreABeA

## Gas fees are high while transacting on Liberty. Why is that?

Liberty 1.0 is not a sharded network and the max TPS it can reach now is 15 TPS. This is typical of testnets. Sharding will be added on Liberty 2.0 release so there will be more stability around gas fees and performance in general.

## What is the use of the faucet and testnet $SHM coins on Liberty?

SHM is the network coin like ETH for Ethereum. SHM is used to pay the gas fees when
you transact on the network for developing dApps and transferring some coins to your friends/community members during alphanet. You simply have to add Liberty network to your wallets like MetaMask and receive 100 testnet $SHM by following the [instructions](https://docs.shardeum.org/basics/claim). As mentioned before developers may qualify for grants while community members will stand a chance to qualify for rewards/airdrops from dApp projects in the future for helping them to stress test their projects. In order to take part in testing out such dApps, $SHM can be very useful to pay transaction fees to carry out a transaction.

## How to find which projects are building on Liberty so I can consider interacting with their interfaces and stand a chance to earn rewards in the future?

Community and developers can easily find the list of projects that are building on Liberty [here](https://docs.google.com/spreadsheets/d/1kthKCY097MlNwfm8g8OAhldmFOE5rTG9vwl7FHrnDEk/edit#gid=907836377).

## How can I promote the project I am building on Shardeum to its community and other developers?

We encourage you to fill up and submit this simple [form](https://forms.gle/BFLXtLn9Urq9KH998) which will be transposed into a spreadsheet in raw form for easy consumption by the Shardeum community and other devs building on the network. This spreadsheet is stored on the project’s google drive which is open to public.

## While I was trying to transfer 50 $SHM to another account of mine, it was successful. However there was another $50 SHM that was deducted from my account? Please look into this serious issue of double spending.

If you attempt to speed up or cancel your transactions on Liberty, they will result in double transfer/duplications. There are no MEVs or gas bribes on the Shardeum network. We will soon put a fix in place to prevent the causality of such double transfers in the later stages of testnet.

## RPC is slow. Is there any ongoing issue?

There are two things to keep in mind here. Liberty phase 1 has a very limited number of unsharded nodes (12 nodes and 2 rotating nodes) and hence limited RPC endpoints, which won’t be sufficient to handle a large number of RPC queries. Our RPC endpoint limits will go away once we deploy larger networks (number of validator resources). RPC queries scale linearly with the number of nodes with or without sharding.  Our transaction limits will further start scaling up as soon as we are ready to enable sharding in alpha-testnet Liberty phase 2.
