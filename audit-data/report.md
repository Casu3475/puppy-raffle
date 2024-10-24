---
title: Protocol Audit Report
author: Romain CASUBOLO
date: November 15, 2024
header-includes:
  - \usepackage{titling}
  - \usepackage{graphicx}
---

<!-- \begin{titlepage}
\centering
\begin{figure}[h]
\centering
\includegraphics[width=0.5\textwidth]{logo.pdf}
\end{figure}
\vspace\*{2cm}
{\Huge\bfseries Protocol Audit Report\par}
\vspace{1cm}
{\Large Version 1.0\par}
\vspace{2cm}
{\Large\itshape Cyfrin.io\par}
\vfill
{\large \today\par}
\end{titlepage}

\maketitle -->

<!-- Your report starts here! -->

Prepared by: [Cyfrin](https://cyfrin.io)
Lead Security Researcher :

- Romain CASUBOLO

# SOMETHING

# Table of Contents

- [SOMETHING](#something)
- [Table of Contents](#table-of-contents)
- [Protocol Summary](#protocol-summary)
- [Disclaimer](#disclaimer)
- [Risk Classification](#risk-classification)
- [Audit Details](#audit-details)
  - [Scope](#scope)
  - [Roles](#roles)
- [Executive Summary](#executive-summary)
  - [Issues found](#issues-found)
- [Findings](#findings)
  - [High](#high)
    - [\[H-1\] Storing the password on-chain makes it visible to anyone, and no longer private](#h-1-storing-the-password-on-chain-makes-it-visible-to-anyone-and-no-longer-private)
    - [\[H-2\] `PasswordStore::setPassword` has no access controls, meaning a non-owner could change the password](#h-2-passwordstoresetpassword-has-no-access-controls-meaning-a-non-owner-could-change-the-password)
  - [Medium](#medium)
  - [Low](#low)
  - [Informational](#informational)
    - [\[I-3\] The `PasswordStore::getPassword` natspec indicates a parameter that doesn't exist, causing the natspect to be incorrect](#i-3-the-passwordstoregetpassword-natspec-indicates-a-parameter-that-doesnt-exist-causing-the-natspect-to-be-incorrect)
  - [Gas](#gas)

# Protocol Summary

# Disclaimer

The Casu team makes all effort to find as many vulnerabilities in the code in the given time period, but holds no responsibilities for the findings provided in this document. A security audit by the team is not an endorsement of the underlying business or product. The audit was time-boxed and the review of the code was solely on the security aspects of the Solidity implementation of the contracts.

# Risk Classification

|            |        | Impact |        |     |
| ---------- | ------ | ------ | ------ | --- |
|            |        | High   | Medium | Low |
|            | High   | H      | H/M    | M   |
| Likelihood | Medium | H/M    | M      | M/L |
|            | Low    | M      | M/L    | L   |

We use the [CodeHawks](https://docs.codehawks.com/hawks-auditors/how-to-evaluate-a-finding-severity) severity matrix to determine severity. See the documentation for more details.

# Audit Details

**The findings described in this document correspond the following commit hash : **

```

```

## Scope

```
./src/
└── PasswordStore.sol
```

## Roles

Owner : the user who can set the password and read the password.
Outsiders: no one else should be able to set or read he password.

# Executive Summary

_add some notes about how the audit went, types of things you found, etc._
_i septnx hours blablabla_

## Issues found

| Severity | Number of issues found |
| -------- | ---------------------- |
| High     | 2                      |
| Medium   | 0                      |
| Low      | 0                      |
| Inf      | 1                      |
| Total    | 3                      |

# Findings

## High

### [H-1] Storing the password on-chain makes it visible to anyone, and no longer private

**Description:** All data stored on-chain is visible to anyone, and can be read directly from the blockchain. The `PasswordStore::s_password` variable is intended to be a private and only accessed through the `PasswordStore::getPassword` function, which is intended to be only called by the owner of the contract.

we show one such method of reading any data off chain below

**Impact:** anyone can read the private password, severly breaking the functionality of the protocol

**Proof of Concept:** (or proof of code)
the below test case shows how anyone can read the password directly from the blockchain.

1. run `anvil` to start a little fake blockchain running

2. deploy the PasswordStore to this locally running blockchain
   `make deploy` doesn't work so
   `forge script script/DeployPasswordStore.s.sol:DeployPasswordStore --rpc-url http://localhost:8545 --private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80 --broadcast`
3. then run the storage tool :
   `cast storage 0x5FbDB2315678afecb367f032d93F642f64180aa3 1 --rpc-url http://127.0.0.1:8545`
   `1` because this is the second variable

4. i get '0x6d7950617373776f726400000000000000000000000000000000000000000014' & you can parse that hex to a string with
   `cast parse-bytes32-string 0x6d7950617373776f726400000000000000000000000000000000000000000014` and you get an output of :

`myPassword`
YEAHHHHHHHHHHHHHHH

**Recommended Mitigation:**
Due to this, the overall architecture of the contract should be rethought. One could encrypt the password off-chain, and then store the encrypted password on-chain. This would require the user to remember another password off-chain to decrypt the password. However, you'd also likely want to remove the view function as you wouldn't want the user to accidentally send a transaction with the password that decrypts your password.

```

```

### [H-2] `PasswordStore::setPassword` has no access controls, meaning a non-owner could change the password

**Description:** The `PasswordStore::setPassword` function is set to be an `external` function, however, the natspec of the function and overall purpose of the smart contract is that `This function allows only the owner to set a new password.`

```javascript
     function setPassword(string memory newPassword) external {
  @>    // @audit - there are no access control
        s_password = newPassword;
        emit SetNetPassword();
    }

```

**Impact:** anyone can set/change the password of the contract, severly breaking the contract intended functionality

**Proof of Concept:** add the following to the `PasswordStore.t.sol` test file

<details>
<summary>code</summary>

```javascript
      function test_anyone_can_set_password(address randomAddress) public {
        vm.assume(randomAddress != owner); // make sure the random address is not the owner
        vm.prank(randomAddress); // Let's pretend that randomAddress is making the next transaction
        string memory expectedPassword = "myNewPassword";
        passwordStore.setPassword(expectedPassword);

        vm.prank(owner);
        string memory actualPassword = passwordStore.getPassword();
        assertEq(actualPassword, expectedPassword);
    }
```

</details>

run `forge test --mt test_anyone_can_set_password`

**Recommended Mitigation:** Add an access control conditional to the `setPaswword` function.

```javascript
    if(msg.sender != s_owner){
      revert PasswordStore_NotOwner();
    }

```

## Medium

## Low

## Informational

### [I-3] The `PasswordStore::getPassword` natspec indicates a parameter that doesn't exist, causing the natspect to be incorrect

**Description:**

```javascript
     /*
     * @notice This allows only the owner to retrieve the password.
     * @param newPassword The new password to set.
     */
     function getPassword() external view returns (string memory) {}

```

The `PasswordStore::getPassword` function signature is `getPassword()` which the natspect say it should be `getPassword(string)`

**Impact:** the napstec is incorrect

**Proof of Concept:**

**Recommended Mitigation:** Remove the incorrect natspect line

```diff
-     * @param newPassword The new password to set.


```

## Gas

<!-- it works with pandoc ! -->
<!-- pandoc report-formatted.md -o report.pdf --from markdown --template=eisvogel --listings -->
