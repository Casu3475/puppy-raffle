### [M-1] Looping through players array to check for duplicates in `PuppyRaffle::enterRaffle` is a potential denial of service (DoS) attack, incrementing gas costs for future entrants

**Description:** The `PuppyRaffle::enterRaffle` function loops through the `players` array to check for duplicates. However, the longer the `PuppyRaffle::players` array is, the more a new player will have to make. This means the gas costs for players who enter right when the raffle stats will be dramatically lower than those who enter later. Every additional address in the `players` array, is an additional check the loop will have to make.

```javascript
// @audit DoS attack
        for (uint256 i = 0; i < players.length - 1; i++) {
            for (uint256 j = i + 1; j < players.length; j++) {
                require(players[i] != players[j], "PuppyRaffle: Duplicate player");
            }
        }
```

**Impact:** the gas costs for raffle entrants will greatly increase as more players enter the raffle. Discouraging later users from entering, and causing a rush at the start of a raffle to be one of the first entrants in the queue.

An attacker might make the `PuppyRaffle::entrants` array so big, that no one else enters, guarenteeing themselves the win.

**Proof of Concept:**

if we have 2 sets of 100 players enter, the gas costs will be as such :
-1st 100 players : 6252128 gas
-2st next players : 18068218 gas

this is more than 3x more expensive for the second 100 players.

add the following test into `PuppyRaffleTest.t.sol`

<details>
<summary>PoC</summary>

```javascript
  function test_DoS() public {

        vm.txGasPrice(1);
        // Let's try to enter 100 players;
        // this is how we create 100 players with different addresses
        uint256 playersNum = 100;
        address[] memory players = new address[](playersNum);
        for (uint256 i = 0; i < playersNum; i++) {
            players[i] = address(i);
        }
        // see how much gas it costs
        uint256 gasStart = gasleft();
        puppyRaffle.enterRaffle{value: entranceFee * players.length}(players);
        uint256 gasEnd = gasleft();

        uint256 gasUsedFirst = (gasStart - gasEnd) * tx.gasprice;
        console.log("Gas used for 100 players: ", gasUsedFirst);


         // now for the 2nd 100 players;
        address[] memory playersTwo = new address[](playersNum);
        for (uint256 i = 0; i < playersNum; i++) {
            playersTwo[i] = address(i + playersNum); // 0, 1, 2 => 100, 101, 102
        }
        // see how much gas it costs
        uint256 gasStartSecond = gasleft();
        puppyRaffle.enterRaffle{value: entranceFee * players.length}(playersTwo);
        uint256 gasEndSecond = gasleft();

        uint256 gasUsedSecond = (gasStartSecond - gasEndSecond) * tx.gasprice;
        console.log("Gas used for the second 100 players: ", gasUsedSecond);

        assert(gasUsedFirst < gasUsedSecond);
        }

```

</details>

test this
`forge test --mt test_DoS -vvv`

**Recommended Mitigation:**

1. consider allowing duplicates. Users can make new wallet addresses anyways, so a duplicate check doesn't prevent the same person from entering multiple times, only the same wallet address.
2. consider using a mapping to check for duplicates. this would allow constant time lookup of whether a user has already entered.

```javascript

    mapping(address => uint256) public addressToRaffleId; // add this
    uint256 public raffleId = 0; // add this

    function enterRaffle(address[] memory newPlayers) public payable {
        require(msg.value == entranceFee * newPlayers.length, "PuppyRaffle: Must send enough to enter raffle"); // modified
        for (uint256 i = 0; i < newPlayers.length; i++) {                                                       // modified
            players.push(newPlayers[i]);                                                                        // modified
            addressToRaffleId[newPlayers[id]] = raffleId; // add this
        }
```

```javascript
 // Check for duplicates only from the new players
       for (uint256 i = 0; i < newPlayers.length; i++) {
               require(addressToRaffleId[newPlayers[i]] != raffleId, "PuppyRaffle: Duplicate player");
           }
       emit RaffleEnter(newPlayers);
   }
```

```javascript
function selectWinner() external {
        raffleId = raffleId + 1; // add this
        require(block.timestamp >= raffleStartTime + raffleDuration, "PuppyRaffle: Raffle not over");
}

```

3. You could use [OpenZeppelin's `EnumerableSet` library] (https://docs.openzeppelin.com/contracts/4.x/api/utils#EnumerableSet)

### [S-#] TITLE (Root Cause + Impact)

**Description:**

**Impact:**

**Proof of Concept:**

**Recommended Mitigation:**
