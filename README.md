![App Screenshot](./images//)

## Document Properties

| Client         | PancakSwap                                     |
| :------------- | :--------------------------------------------- |
| Title          | Smart Contract Audit Report                    |
| Target         | PancakeSwap Prediction V2                      |
| Version        | 1.0                                            |
| Author         | [Idogwu Chinonso](https://github.com/Nonnyjoe) |
| Classification | Public                                         |

| Version | Date           | Author                                         | Description   |
| :------ | :------------- | :--------------------------------------------- | :------------ |
| 1.0     | April 01, 2023 | [Idogwu Chinonso](https://github.com/Nonnyjoe) | Final Release |

<p id="dsds">1.0 INTRODUCTION </p>

### 1.1 Disclaimer

The smart contract audit I carried out is based on the information provided to me and my expertise in the field. It is not a guarantee of the security or functionality of the smart contract, and it does not eliminate all potential risks. Cryptocurrency investments come with inherent risks, and I am not liable for any financial losses that may occur as a result of investing in a smart contract or any other cryptocurrency-related activities. Investors are advised to do their own research and due diligence before making any investment decisions.

### 1.2 🚀 About Me

Hello, my name is Idogwu Chinonso and I am a Smart Contract Developer. Currently, I'm interning at Web3bridge where I am working to expand my knowledge and skills in the field of blockchain development.

As a developer, I am passionate about creating secure and efficient smart contracts that can help revolutionize the way we interact with technology. I am constantly learning and exploring new technologies.

### 1.3 🛠 Skills

Solidity, Diamond standard implementation, Foundry, Hardhat, Wagmi, Ethers.Js library, Javascript, HTML, CSS ...

### 1.4 🔗 Links

[![portfolio](https://img.shields.io/badge/my_portfolio-000?style=for-the-badge&logo=ko-fi&logoColor=white)](https://nonnyjoe.github.io/my-portfolio/)
[![linkedin](https://img.shields.io/badge/linkedin-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/idogwu-chinonso-a9117b1a3/)
[![twitter](https://img.shields.io/badge/twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white)](https://twitter.com/ChinonsoIdogwu)

### 1.5 PancakeSwap Prediction contract

Pancakeswap Prediction is a decentralized application (dApp) built on the Binance Smart Chain (BSC) that allows users to predict the possible price change of tokens(BNB and CAKE) and earn rewards for their accurate predictions. Users must connect their BSC wallet to the platform, Stake Binance Coin (BNB) or Pancake token (CAKE) to make prediction, and earn a reward in BNB or CAKE if their prediction turns out to be correct.

### 1.6 Scope

_(**Table: 1.6**: PancakeSwap Prediction V2 Audit Scope)_
| Files in scope | SLOC |
| :-------- | :------- |
| Contracts: 1 | |
| `PancakePredictionV3.sol` | `669` |
| | |
| Imports: 6 | |
| `Ownable.sol` | `83` |
| `Pausable.sol` | `105` |
| `ReentrancyGuard.sol` | `69` |
| `IERC20.sol` | `82` |
| `SafeERC20.sol` | `116` |
| `AggregatorV3Interface.sol` | `32` |

### 1.7 Roles

- #### Operator

  - Execute round.
  - Start genesis round.
  - Lock genesis round.
  - Pause subsequent contract interaction.
  - Unpause previous pause event.

- #### Admin

  - Performs all the duties of an Operator.
  - Claim treasury.
  - Set state variable eg. Buffer time, interval time, minimum net, operator, oracle, oracle update allowance, tresury fee.

- #### User
  - Call the betBear or betBull function to place bets.
  - Call the claimable function to check if he won a particular round.
  - Call the refundable function to claim refunds from the contract.
  - Call the claim function to claim rewards for a correct prediction.
  - Call the getuserRounds function to display a list of all rounds the user participated in.
  - call the getUserRoundsLength function to get the number of rounds a user participated in.

### 1.8 System Overview

- #### PancakePredictionV3.sol

  - This is a contract that allows users to stake tokens on their prediction and gain rewards or lose their stakes depending on the outcome of their prediction.

- #### Ownable.sol.
  - This is a contract that defines a single owner who has certain privileges such as being able to transfer ownership to another address.
- #### Pausable.sol.
  - This is a contract that allows the owner to pause and unpause the contract functionality in case of emergencies or maintenance needs.
- #### ReentrancyGuard.sol.
  - This contract provides protection against reentrant attacks, which occur when an attacker tries to execute the same function multiple times before the first execution is complete.
- #### IERC20.sol.
  - This is an interface for an ERC20 token, which defines the basic functions and events for a standard token, such as transfer and approval.
- #### SafeERC20.sol.
  - This contract provides additional safety checks when interacting with ERC20 tokens to avoid common errors such as overflows or underflows.
- #### AggregatorV3Interface.sol.
  - This is an interface for Chainlink's price feed oracle, which provides real-time price data for various assets.

## 2.0 CONTRACT REVIEW

The following functions are part of the pancakeSwap prediction contract and are all carefully and thoroghly intertwined for optimum performance.

### 2.1 genesisStartRound ():

The genesisStartRound function is used to start the genesis round. This function can be called by either the admin or the operator and it requires that genesisStartRound has not been called before. The function increments the current epoch, starts the round by calling the \_startRound function, and sets the genesisStartOnce variable to true.

### 2.2 genesisLockRound ():

The genesisLockRound function is used to lock the genesis round. This function can only be called by the operator and it requires that the genesisStartRound function has already been triggered and that genesisLockRound has not been called yet. The function obtains the current round ID and price from the oracle, and then calls the \_safeLockRound function to lock the current round. After that, it increments the current epoch and starts the next round by calling the \_startRound function, and finally sets the genesisLockOnce variable to true.

### 2.3 Function betBear():

This function is a part of the smart contract for the pancakeSwap prediction contract that allows players to place bets on the outcome of a game round. Specifically, the betBear function allows a player to bet a certain amount on the "bear" position for a given game round.
The function takes two parameters: epoch, which represents the game round, and \_amount, which is the amount of tokens the player wishes to bet. It then performs several checks to ensure the bet is valid and meets the game rules:

If all the checks pass, the function transfers the bet amount from the player's address to the contract address using the safeTransferFrom function of the bet token contract. It then updates the round data by adding the bet amount to the total amount and bear amount for the round. The function also updates the player's betting data by setting their position as "bear", their betting amount, and adding the round to their list of participated rounds. Finally, the function emits a BetBear event, indicating that the player has placed a bear bet for the given round.

### 2.4 Function betBull():

The betBull is a function similar to the betBear, it also takes two parameters similar to the betBear, runs several checks to ensure the bet is valid and meets the game rules:

If all the checks pass, the function transfers the bet amount from the player's address to the contract address using the safeTransferFrom function of the bet token contract. It then updates the round data by adding the bet amount to the total amount and Bull amount for the round. The function also updates the player's betting data by setting their position as "Bull", their betting amount, and adding the round to their list of participated rounds. Finally, the function emits a BetBull event, indicating that the player has placed a bull bet for the given round.

### 2.5 Function Claim():

The purpose of the claim function is to allow users to claim rewards or refunds for their bets in specified epochs. The function takes an array of epoch numbers as input and loops through each epoch to determine if the user is eligible for a reward or refund.
If the epoch is valid and the oracle sent in the close price for the round, then the user is eligible for a reward if he placed a correct prediction. The amount of reward is calculated based on the user's bet amount and the reward parameters for the round. If the epoch is invalid or the oracle has not yet called the result, then the user is eligible for a refund of their bet amount.
After determining the reward or refund amount for each epoch, the function updates the user's ledger to indicate that they have claimed their reward or refund, and adds the amount to the total reward. If the total reward is greater than zero, the function transfers the reward amount to the user's account.

### 2.6 executeRound():

This function is used to start, lock the price, and end rounds of a game round(epoch). It is only callable by the operator of the game.
The function first checks if the genesis start and lock rounds have been triggered, which means that the current round is not the first round and the price for the first round has been locked.
Then, it gets the current round ID and price from the oracle and sets the oracleLatestRoundId variable to the current round ID.
Next, the function calls the \_safeLockRound function to lock the price for the current round (n-1), and \_safeEndRound function to end the previous round (n-2) with the current price.
After that, the function calls the \_calculateRewards function to calculate rewards for the previous round (n-1).
Finally, the function increments the currentEpoch variable to the current round (n) and calls the \_safeStartRound function to start the new round (n).

### 2.7 pause():

This function is called by the admin or operator to pause the contract. When the contract is paused, no further rounds can be started or ended, and the price can't be updated. The function calls \_pause() which is an internal function of the OpenZeppelin Pausable contract to set the paused state to true. An event Pause is emitted with the current epoch number.

### 2.8 claimTreasury():

This function is called by the admin to claim all the Tresury fee that have accumulated in the contract's treasury. The treasuryAmount variable is set to zero after the transfer of funds to the adminAddress. The function uses the SafeERC20 library to transfer the funds from the contract's balance to the adminAddress. An event TreasuryClaim is emitted with the amount of tokens transferred.

### 2.9 unpause():

This function is called by the admin or operator to unpause the contract. When the contract is unpaused, rounds can be started and ended again, and the price can be updated. The function calls \_unpause() which is an internal function of the OpenZeppelin Pausable contract to set the paused state to false. Additionally, it resets the genesis state by setting genesisStartOnce and genesisLockOnce to false. Therefore sunsequent rounds will have to be created as genesis round. An event Unpause is emitted with the current epoch number.

### 2.10 setBufferAndIntervalSeconds():

This function allows the admin to set the buffer and interval time for each betting round. The buffer time is the time period before the start of each round during which no bets can be made, and the interval time is the time period between the end of one round and the start of the next round. This function can only be called when the contract is in a paused state, and only by the admin.

### 2.11 setMinBetAmount():

This function allows the admin to set the minimum bet amount for each round. This is the minimum amount that a user must bet in order to participate in a round. The function can only be called when the contract is paused, and only by the admin.

### 2.12 setOperator():

This function allows the admin to set the address of the operator. The operator is responsible for triggering the start and lock of each betting round. This function can only be called by the admin.

### 2.13 setOracle():

This function allows the admin to set the address of the price oracle that the contract uses to fetch the current BNB/USD or CAKE/USD price. This function can only be called when the contract is paused, and only by the admin. After setting the new oracle address, the function checks whether the new oracle implements the necessary function latestRoundData().

### 2.14 setOracleUpdateAllowance():

This function sets the allowance for the Oracle to update the round data. It can be called only by the admin when the contract is paused. The \_oracleUpdateAllowance parameter specifies the maximum number of updates allowed by the Oracle in one round.

### 2.15 setTreasuryFee():

This function sets the treasury fee that is charged on every bet. It can be called only by the admin when the contract is paused. The \_treasuryFee parameter specifies the percentage of the bet amount that will be charged as the treasury fee. The maximum allowed treasury fee is specified by the MAX_TREASURY_FEE constant.

### 2.16 recoverToken():

This function allows the owner to recover tokens sent to the contract by mistake. The \_token parameter specifies the address of the token that needs to be recovered, and the \_amount parameter specifies the amount of tokens to be recovered. This function can be called only by the owner.

### 2.17 setAdmin():

This function sets the admin address. The \_adminAddress parameter specifies the new address that will become the admin. This function can be called only by the owner.

### 2.18 function getUserRounds() :

This function returns an array of BetInfo and their corresponding round IDs for a given user, starting from a given cursor index and returning at most size entries. It is used to provide a user's betting history.

### 2.19 function getUserRoundsLength():

This function returns the number of rounds a user has participated in.

### 2.20 function claimable():

This function checks if a user is eligible to claim a reward for a specific epoch. It returns true if the oracle has been called for the epoch, the user has placed a bet, the bet has not yet been claimed, and the user's position matches the direction of the price change between the lock and close times.

### 2.21 function refundable():

This function checks if a user is eligible for a refund for a specific epoch. It returns true if the oracle has not been called for the epoch, the user has placed a bet, the bet has not yet been claimed, and the epoch has passed the buffer time (specified by bufferSeconds) since the round's close time.

### 2.22 \_calculateRewards():

This function is used to calculate the rewards for a round after it has ended. It takes the epoch as an input, which is used to retrieve the relevant round from the rounds mapping. It checks if rewards have already been calculated for this round by checking if the rewardBaseCalAmount and rewardAmount variables are both zero. If they are, it calculates the reward amounts based on whether the bull or bear position won or if it was a draw (i.e., house wins). The rewardBaseCalAmount is set to the amount of the winning position, and the rewardAmount is set to the total amount bet minus the treasury fee. The treasury fee is calculated as a percentage of the total amount bet and is stored in the treasuryFee variable. The treasuryAmt variable is calculated as the amount of the treasury fee, and the treasuryAmount variable is updated to reflect this.

### 2.23 \_safeEndRound():

This function is used to end a round safely. It takes the epoch, roundId, and price as inputs. The epoch is used to retrieve the relevant round from the rounds mapping. It checks that the round has locked and that the current block timestamp is after the close timestamp but before the close timestamp plus the buffer seconds. It sets the closePrice and closeOracleId variables of the round to the given price and roundId, respectively, and sets the oracleCalled flag to true. Finally, it emits an EndRound event to notify other contracts of the round's end.

### 2.24 \_safeLockRound():

This function is used to lock a round, meaning that users can no longer place bets on the round. It takes the epoch number, round ID, and price of the round as parameters. Before locking the round, the function performs several checks to ensure that the round can be locked. It requires that the round has already started, the current time is greater than or equal to the lock timestamp, and that the current time is within the bufferSeconds from the lock timestamp. If all checks pass, the round is locked by setting the close timestamp, lock price, and lock oracle ID. An event is emitted to notify listeners that the round has been locked.

### 2.25 \_safeStartRound(uint256 epoch):

This function is used to start a new round. It takes the epoch number as a parameter. Before starting the round, the function performs several checks to ensure that the previous round (n-2) has ended. It requires that the genesisStartOnce flag has been triggered, the previous round n-2 has ended, and that the current time is greater than or equal to the close timestamp of the previous round n-2. If all checks pass, the function calls \_startRound(epoch) to start the new round.

### 2.26 \_startRound():

This function is called by \_safeStartRound() to actually start a new round. It takes the epoch number as a parameter. When called, it initializes the Round struct for the new round by setting the start timestamp, lock timestamp, close timestamp, epoch number, and total amount to 0. An event is emitted to notify listeners that the new round has started.

### 2.27 \_bettable():

This is an internal view function that checks whether the specified epoch is currently in a "bettable" state. An epoch is bettable if it has started (startTimestamp is not 0), the locking phase has not ended (lockTimestamp is not 0), and the current time is between the start and lock timestamps. If all of these conditions are met, the function returns true, otherwise it returns false.

### 2.28 \_getPriceFromOracle():

This is an internal view function that retrieves the latest price data from the chainlink oracle and checks whether it is valid. Specifically, it checks that the timestamp of the price data is within the allowed update allowance (oracleUpdateAllowance) from the current block timestamp, and that the round ID of the price data is larger than the last recorded round ID (oracleLatestRoundId). If the price data is deemed valid, the function returns a tuple containing the round ID and price.

### 2.29 \_isContract(address account):

This is an internal view function that checks whether the specified address is a smart contract or an externally owned account (EOA). It does this by checking the size of the bytecode at the specified address using the extcodesize opcode. If the bytecode size is greater than 0, the function returns true, indicating that the address is a contract. Otherwise, it returns false, indicating that the address is an EOA.

### SUMMARY

The contract is for a prediction market, where users can bet on the outcome of the price of BNB or CAKE in the future. The events are grouped into epochs. Each round is associated a specific time period.
Users can place bets on the outcome of a round by calling the betBear or betBull function, which takes the epoch, round ID, and outcome as parameters. The amount bet is transferred to the contract, and the user's bet is recorded.
Once the round is complete, the executeRound function is called to determine and record the outcome of the round.
Users can withdraw their winnings by calling the claim function, which transfers their reward to their account.
There are also several internal functions that are used to manage the rounds and ensure that bets can only be placed during certain time periods. These functions include \_bettable, \_safeLockRound, \_safeStartRound, and \_startRound.
The contract also includes some additional features, such as the ability to cancel a round and a check to ensure that a user is not a contract.

## 3.0 FINDINGS

### 3.1 Summary

_(**Table: 3.1**: PancakeSwap Prediction V2 Summary of Findings)_
| Severeity | Number of Findings |
| :-------- | :------- |
| High | 0|
| Medium | 0 |
| Low| 1 |
| Informational | 4 |
| Undetermined| 0 |
| Total| 5 |

### 3.2 Qualitative Analysis

_(**Table: 3.2**: PancakeSwap Prediction V2 Qualitative Analysis)_
| Metric | Rating | Comment |
| :-------- | :------- | :----- |
| Code Complexity | Excellent | Functionality is very simple and organized |
| Documentation | Moderate | Documentation could be improved |
| Best Practices | Moderate | Some best practices were implemented but also found lacking in certain areas |

### 3.3 Key Findings

Overall, these smart contracts are well-designed and engineered, though the implementation can be
improved by resolving the identified issues (shown in Table 3.3), including 1 Medium-severity vulnerability,
and 4 informational recommendations.

_(**Table: 3.3**: PancakeSwap Prediction V2 Audit Key Findings)_
| ID | Severity | Title | Category |
| :-------- | :------- | :----- | :----- |
| `LW-001` | `Low` | Missing address(0) check in constructor | `Security Feauture` |
| `IN-001` | `Informational` | Function claim() reverts without a specific pointer | `Coding Practice` |
| `IN-002` | `Informational` | BetBull/ BetBear Epoch errors could be more specific | `Coding Practice` |
| `IN-003` | `Informational` | Redundant State/ code removal in BetBear/BetBull | `Coding Practice` |
| `IN-004` | `Informational` | Redundant State/ code removal in Claim() function | `Coding Practice` |

## 4.0 DETAILED RESULT

### 4.1 Missing address(0) check in constructor

|                                 |                               |                               |
| :------------------------------ | :---------------------------- | :---------------------------- |
| `LW-001`                        | `Target: PancakePredictionV2` | `Category: Security Features` |
| `Severity: Low`                 | `Likelihood: Low`             | `Impact: Medium`              |
| `File: PancakePredictionV3.sol` | `Instances: 3`                |                               |

### Description

In the pancakeSwap PredictionV2 prediction contract, there is a missing zero-address check in the constructor and this could lead to a potential loss of countrol in the contract.
![App Screenshot](./images/constructor_no_address_check.png)

### Recomendation

Checks to ensure that the address field is not empty or specified as address zero should be implemented.

### 4.2 Function claim() reverts without a specific pointer

|                                 |                               |                             |
| :------------------------------ | :---------------------------- | :-------------------------- |
| `IN-001`                        | `Target: PancakePredictionV2` | `Category: Coding Practice` |
| `Severity: Informational`       | `Likelihood: N/A`             | `Impact: N/A`               |
| `File: PancakePredictionV3.sol` |                               |                             |

### Description

In the pancakeSwap PredictionV2 prediction contract, the claim() function is designed to take in an array of Epoch and loop through each epoch to determine if they are claimable, I noticed that the function will revert if just one out of five correct epoch is wrong without specifying the wrong epoch.

![App Screenshot](./images/CLAIM%20FUNCTION.png)

### Recomendation

Replace revert with a more specific custom error. eg:

```bash
  contract test {
    uint[] numbers = [12, 9, 21, 22, 76];
    error InsufficientBalance(uint position, uint withdrawAmount);

    function testError() public view {
        for(uint i; i<= numbers.length; i++) {
            if (numbers[i] < 10) revert InsufficientBalance( {position: i, withdrawAmount: numbers[i]});
        }
    }
}
```

### 4.3 BetBull/ BetBear Epoch errors could be more specific

|                                 |                               |                             |
| :------------------------------ | :---------------------------- | :-------------------------- |
| `IN-002`                        | `Target: PancakePredictionV2` | `Category: Coding Practice` |
| `Severity: Informational`       | `Likelihood: N/A`             | `Impact: N/A`               |
| `File: PancakePredictionV3.sol` |                               |                             |

### Description

In the pancakeSwap PredictionV2 prediction contract, the betBear() and betBull() function there is a require statement in line `163` , this checks that the inputed epoch is the current available epoch, but fails to specify the actual reason for the error.

![App Screenshot](./images/betBear.png)

### Recomendation

Replace the require statement in concern with a conditional statement eg.

```bash
if (epoch > currentEpoch), revert ("too early");
if (epoch < currentEpoch), revert ("too late");
```

### 4.4 Redundant State/ code removal in BetBear/BetBull

|                                 |                               |                             |
| :------------------------------ | :---------------------------- | :-------------------------- |
| `IN-003`                        | `Target: PancakePredictionV2` | `Category: Coding Practice` |
| `Severity: Informational`       | `Likelihood: N/A`             | `Impact: N/A`               |
| `File: PancakePredictionV3.sol` |                               |                             |

### Description

In the pancakeSwap PredictionV2 prediction contract, the betBear() and betBull() function's first four lines and other lines are identical and could be packed into a single function to be called from the both functions.

![App Screenshot](./images/betBear.png)

### Recomendation

Collaps the identified lines into a function and call the function from the two functions (betBull & betBear).

### 4.5 Redundant State/ code removal in Claim() function

|                                 |                               |                             |
| :------------------------------ | :---------------------------- | :-------------------------- |
| `IN-004`                        | `Target: PancakePredictionV2` | `Category: Coding Practice` |
| `Severity: Informational`       | `Likelihood: N/A`             | `Impact: N/A`               |
| `File: PancakePredictionV3.sol` |                               |                             |

### Description

In the pancakeSwap PredictionV2 prediction contract, the claim() function in line `241` uses a redundant if check to confirm that the reward is greater than zero, this is redundant and just extra computation because the trasfer function of tokens already has this check and the function will always revert from line `225` if the users reward is zero(0).
![App Screenshot](./images/CLAIM%20FUNCTION2.png)

### Recomendation

Remove the redundant if check from the claim function.

## 5.0 Conclusion

In this audit, I have analyzed the PancakeSwap prediction V2 design and implementation. PancakeSwap Prediction V2 protocol is one of the core functions of PancakeSwap, which is
designed as a decentralized BNB and CAKE price prediction platform. It allows the user to profit from the BNB, CAKE
price rises and falls. This protocol enriches the PancakeSwap ecosystem and also presents a unique contribution to the current DeFi ecosystem.

The current code base is well structured and neatly organized. Those identified issues could serve as a correction for future implementations.

## Acknowledgements

- [Pancakeswap prediction documentation](https://docs.pancakeswap.finance/products/prediction)
- [PancakeSwap PredictionV2 Audit](https://github.com/peckshield/publications/blob/master/audit_reports/PeckShield-Audit-Report-PancakeSwap-PredictionV2-v1.0.pdf)
