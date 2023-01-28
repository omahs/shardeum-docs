---
title: Same Address Cross Chain
sidebar_position: 5
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

## Keep track of the same contracts cross chain easily

Having contracts deployed at the same address cross chain makes it very easy to find contracts for:

    -users who want to quickly find the same contracts on other chains
    -developers maintaining the same contracts across different chains

## Simple deployment with the same address cross chain

Simple methods:

    -a new wallet (nonce = 0 for all chains)
    -a wallet used for deploying contracts with the same cross chain nonce values

:::caution Caution
Using the same wallet again will require you to keep track of nonce values cross chain.
:::

Alchemy recommends using a Hardhat nonce query script to quickly check a wallet's nonce values cross chain:

[Create a Hardhat task to query the nonce](https://docs.alchemy.com/docs/how-to-deploy-a-contract-to-the-same-address-on-multiple-networks#create-a-hardhat-task-to-query-the-nonce)


## How are smart contract addresses determined?

Smart contracts deployment addresses can be created with either opcodes:

    CREATE

or

    CREATE2

## How does CREATE determine smart contract addresses?

CREATE deployment addresses depend on the sender's:

    -address
    -nonce

## How does CREATE2 determine smart contract addresses?

CREATE2 deployment addresses depend on:

    -sender address (CREATE2 Factory is the sender in this context to execute the CREATE2 opcode)
    -salt
    -byteCode [creationCode since constructor input arguments can change the byteCode value]

For more info on the CREATE2 opcode:

[EIP-1014: Skinny CREATE2](https://eips.ethereum.org/EIPS/eip-1014)

## Can you deploy a contract to the same address twice?

Yes (for some contracts).

Do the following steps:

1. Create a contract with SELFDESTRUCT in a function only the owner can access to keep the contract secure.

:::warning WARNING
Calling SELFDESTRUCT will delete all data in a contract! Be very careful using it.
Goerli supports SELFDESTRUCT while Shardeum currently does not. SELFDESTRUCT might
also be deprecated with EIP-4758: https://eips.ethereum.org/EIPS/eip-4758.
:::

2. Deploy a contract using a CREATE2 Factory.

:::caution Caution
Remember this CREATE2 Factory address and the salt you used for the contract byteCode deployed.
:::

3. Once the contract is deployed from the CREATE2 Factory, call SELFDESTRUCT.

:::warning WARNING
If you skip this step 3, you will see the following error on the Shardeum explorer:

      create collision

since there is a contract at that address that hasn't been destroyed yet.
:::

4. Use the same CREATE2 Factory contract at the same address to deploy the contract with the same byteCode and salt.

Doing this will kill a contract at an address, then bring it back to life at the same address.

Examples:

[Contract deployed with CREATE that did a SELFDESTRUCT](https://goerli.etherscan.io/address/0x941642335762c6c9d35ef35bc6591c0c41d63a0c#code)

[Contract as a CREATE2 Factory](https://goerli.etherscan.io/address/0xDf258F3868d67D802f2eb4FA7e64d61Ff96Fa72E#code)

[Contract that was deployed with CREATE2, then called SELFDESTRUCT, then was brought back to life with the above CREATE2 Factory](https://goerli.etherscan.io/address/0x1b6C44eD7fACDAF2bc8770E207d204bb2742f1d3)


## CREATE2 General Factory

This factory is useful for:

    -saving gas:
      -factory can be used for other contracts since it takes raw creationCode
      -constructors cannot be payable (callvalue() [msg.value in assembly] set to 0, saves gas)
      -constructors cannot have arguments (memory variables not used saves gas)
    -creationCode == runtimeCode, since there are no constructor arguments changing the byteCode

<Tabs>
  <TabItem value="solidity" label="Solidity" default>

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.17;

error notOwner();

contract ContractToDeployMetamask { //Bytecode matches creation code with no constructor input arguments. Bytecode with tutorial address 0xc1202e7d42655F23097476f6D48006fE56d38d4f:
                                    //0x60a060405234801561001057600080fd5b5073c1202e7d42655f23097476f6d48006fe56d38d4f73ffffffffffffffffffffffffffffffffffffffff1660808173ffffffffffffffffffffffffffffffffffffffff16815250506080516102c96100806000396000818160bb0152818160df015261016401526102c96000f3fe608060405234801561001057600080fd5b506004361061004c5760003560e01c806321cf146c14610051578063b4a99a4e1461006f578063f81bfa3f1461008d578063f965e32e14610097575b600080fd5b6100596100b3565b60405161006691906101be565b60405180910390f35b6100776100b9565b604051610084919061021a565b60405180910390f35b6100956100dd565b005b6100b160048036038101906100ac9190610266565b61019b565b005b60005481565b7f000000000000000000000000000000000000000000000000000000000000000081565b7f000000000000000000000000000000000000000000000000000000000000000073ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff1614610162576040517f251c9d6300000000000000000000000000000000000000000000000000000000815260040160405180910390fd5b7f000000000000000000000000000000000000000000000000000000000000000073ffffffffffffffffffffffffffffffffffffffff16ff5b8060008190555050565b6000819050919050565b6101b8816101a5565b82525050565b60006020820190506101d360008301846101af565b92915050565b600073ffffffffffffffffffffffffffffffffffffffff82169050919050565b6000610204826101d9565b9050919050565b610214816101f9565b82525050565b600060208201905061022f600083018461020b565b92915050565b600080fd5b610243816101a5565b811461024e57600080fd5b50565b6000813590506102608161023a565b92915050565b60006020828403121561027c5761027b610235565b5b600061028a84828501610251565b9150509291505056fea2646970667358221220f13ed2f135ac0e33e3f372e325eaa526fa74eefc16e73495164e7b2549f0974464736f6c63430008110033
    uint public storageSlot0;
    address public immutable Owner;

    constructor() { // Put your address as Owner instead of msg.sender because msg.sender will be another contract with CREATE2 salt deploy.
        Owner = 0xc1202e7d42655F23097476f6D48006fE56d38d4f; // Metamask address for tutorial.
    }                                                       // Do not do tx.origin because someone can front run you during contract creation!

    function changeValue(uint updateStorageSlot) public {
        storageSlot0 = updateStorageSlot;
    }

    function killThisContract() public { //After a contract is killed at an address, you can deploy another contract at that address.
        if(msg.sender != Owner) { revert notOwner(); }
        selfdestruct(payable(Owner)); //Deletes all data from this contract address. Address inside selfdestruct call gets all ether in the contract self destructing.
    }                                

}

contract Create2Factory {

    event create2Event(address deployedAddressEvent);

    function generalDeployCreate2(uint _salt, bytes memory creationCode) public { // BYTECODE NEEDS TO HAVE "0X" PREFIX TO BUILD RLP!
        address deployedAddress;     // Salt example:         0x0000000000000000000000000000000000000000000000000000000000000000
        assembly {                   // Another salt example: 0x123456789abcdef0000000000000000000000000000000000000000000000000
            deployedAddress := create2(0, add(creationCode, 0x20), mload(creationCode), _salt) //callvalue() is the same as msg.value in assembly. callvalue() == 0 to save gas since the constructor and this create2 function are not payable.
            if iszero(extcodesize(deployedAddress)) { //Note: constructor is not payable and takes no input variables (hardcoded in) to keep the creation code simple for this example.
                revert(0, 0)                           
            }
        }
        emit create2Event(deployedAddress);
    }

    function generalPrecomputeAddress(uint _salt, bytes memory creationCode) public view returns (address) { // BYTECODE NEEDS TO HAVE "0X" PREFIX TO BUILD RLP!
        bytes32 hash = keccak256(abi.encodePacked(bytes1(0xff), address(this), _salt, keccak256(creationCode)));
        return address(uint160(uint(hash)));
    }

}
```

  </TabItem>
</Tabs>


## CREATE2 No Bytecode Function Input

This factory is useful for:

    -building creationCode directly inside CREATE2 Factory functions with constructor argument values
    -keeping contract deployment simple without needing to deploy other contracts with general byteCode

<Tabs>
  <TabItem value="solidity" label="Solidity" default>

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.17;

error notOwner();

contract ContractToDeploy {

    uint public storageSlot0;
    address public immutable Owner;

    constructor(address inputAddress) payable {
        Owner = inputAddress; //Need to put address as input instead of msg.sender because msg.sender will be another contract with CREATE2 salt deploy.
    }

    function changeValue(uint updateStorageSlot) public {
        storageSlot0 = updateStorageSlot;
    }

    function killThisContract() public { //After a contract is killed at an address, you can deploy another contract at that address.
        if(msg.sender != Owner) { revert notOwner(); }
        selfdestruct(payable(Owner)); //Deletes all data from this contract.
    }

}

contract Create2Factory { //Modified from: "Create2 | Solidity 0.8" by  Smart Contract Programmer https://www.youtube.com/watch?v=883-koWrsO4&ab_channel=SmartContractProgrammer

    event create2Event(address deployedAddressEvent);

    function create2DeployContract(bytes32 _salt, address ownerAddress) public payable {                              // Salt example:  0x0000000000000000000000000000000000000000000000000000000000000000
        ContractToDeploy deployedAddress = new ContractToDeploy {value: msg.value, salt: _salt}(ownerAddress); // Another salt example: 0x123456789abcdef0000000000000000000000000000000000000000000000000
        emit create2Event(address(deployedAddress));
    }

    function precomputeAddress(uint _salt, address ownerAddress) public view returns (address) {
        bytes memory creationCodeValue = abi.encodePacked(type(ContractToDeploy).creationCode, abi.encode(ownerAddress));
        bytes32 hash = keccak256(abi.encodePacked(bytes1(0xff), address(this), _salt, keccak256(creationCodeValue)));
        return address(uint160(uint(hash)));
    }

}
```

  </TabItem>
</Tabs>
