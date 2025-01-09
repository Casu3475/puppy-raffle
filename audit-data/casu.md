for details, see https://immunefisupport.zendesk.com/hc/en-us/articles/12435277406481-Bug-Report-Template

### [M-1] `enterRaffle` function uses gas inefficient duplicate check that causes leads to Denial of Service, making subsequent participants to spend much more gas than previous users to enter.

**Description:** In the `enterRaffle` function, to check duplicates, it loops through the `players` array. As the `player` array grows, it will make more checks, which leads the later user to pay more gas than the earlier one. More users in the Raffle, more checks a user have to make leads to pay more gas.

**Impact:** As the arrays grows significantly over time, it will make the function unusable due to block gas limit. This is not a fair approach and lead to bad user experience.

**Proof of Concept:** In existing test suite, add this test to see the difference b/w gas for users.
once added run `forge test --mt testEnterRaffleIsGasInefficient -vvvvv` in terminal. you will be able to see logs in terminal.

```javascript
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

**Recommended Mitigation:**

## [H-1] Likelihood & Impact:

-Impact ? HIGH
Are the funds directly at risk ? no
Severe disruption of protocol functionality ? YES
-Likelihood : HIGH
-so severity : HIGH
