# Hermez Network Fourth Phase2 Trusted Setup Ceremony.

This repository contains the transcript of the Phase2 Ceremony.

This ceremony runs the trusted setup for 3 circuits:

* **circuit-1912-32-256-64**
* **circuit-344-32-256-64**
* **withdraw**

The first two circuits are the validation circuits for 2048Tx and 400 Txs.  The third circuit is the one used in the rollup exits.

See [Hermez Documentation](https://docs.hermez.io/#/) for fore information of how the rollup works.

The Circom circuits can be found in [circuits](https://github.com/hermeznetwork/circuits).

For this ceremony we used the tag `phase2ceremony_3` [circuits](https://github.com/hermeznetwork/circuits) with repo commit value  `45a486464334e08bd5da948ffb2099fcf1dc7c2f`

For contributing to the ceremony see: [CONTRIBUTE.md](CONTRIBUTE.md)

For verifying the ceremony see: [VERIFY.md](VERIFY.md)

## Contributions

### 2048 Tx Circuit

| Atestation<br>&nbsp; | Contribution File<br>&nbsp; | Contributor<br>&nbsp; | Contribution Hash &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; |
|:-----|:------------ |:-----|:--------------------------------------|
| 0000_initial | [circuit-2048-32-256-64_hez4_0000.zkey](https://hermez.s3-eu-west-1.amazonaws.com/circuit-2048-32-256-64_hez4_0000.zkey)     | |
| [0001_jordi](https://github.com/hermeznetwork/phase2ceremony_4/tree/main/0001_jordi) | [circuit-1912-32-256-64_hez4_0001.zkey](https://hermez.s3-eu-west-1.amazonaws.com/circuit-2048-32-256-64_hez4_0001.zkey)     | [jordi](https://keybase.io/jbaylina)  | `b1a8af44 0d3391ec`<br>`33fe8c8b 799125d6`<br>`49b9d6ee 68357563`<br>`162bfdc1 787e7496`<br>`8bafea5e 71415517`<br>`33ad21e5 4b2e96fc`<br>`0e1de40b 4691e5d6`<br>`ac98b56b 60103914` |


### 400 Tx Circuit

| Atestation<br>&nbsp; | Contribution File<br>&nbsp; | Contributor<br>&nbsp; | Contribution Hash &nbsp; &nbsp; &nbsp; &nbsp; |
|:-----|:------------ |:-----|:--------------------------------------|
| 0000_initial | [circuit-400-32-256-64_hez4_0000.zkey](https://hermez.s3-eu-west-1.amazonaws.com/circuit-400-32-256-64_hez4_0000.zkey)     | |
| [0001_jordi](https://github.com/hermeznetwork/phase2ceremony_4/tree/main/0001_jordi) | [circuit-400-32-256-64_hez4_0001.zkey](https://hermez.s3-eu-west-1.amazonaws.com/circuit-400-32-256-64_hez4_0001.zkey)     | [jordi](https://keybase.io/jbaylina)  | `50db83d2 66932fda`<br>`5b2a954c 78834f93`<br>`81d2357f cc5a23e8`<br>`535abfb1 39ca4ff9`<br>`770c81fc 0af7b824`<br>`54db886e ec7b00eb`<br>`0a98a561 8421ca7a`<br>`2e861a1c 6503847f` |

### WithdrawCircuit

| Atestation<br>&nbsp; | Contribution File<br>&nbsp; | Contributor<br>&nbsp; | Contribution Hash &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp; &nbsp; <br> &nbsp; |
|:-----|:------------ |:-----|:--------------------------------------|
| 0000_initial | [withdraw_hez4_0000.zkey](https://hermez.s3-eu-west-1.amazonaws.com/withdraw_hez4_0000.zkey)     | |
| [0001_jordi](https://github.com/hermeznetwork/phase2ceremony_4/tree/main/0001_jordi) | [withdraw_hez4_0001.zkey](https://hermez.s3-eu-west-1.amazonaws.com/withdraw_hez4_0001.zkey)     | [Jordi](https://keybase.io/jbaylina)  |     `aa4f07ec 628e9875`<br>`42cdfba7 19057c45`<br>`7c0980a0 f3aa23ba`<br>`c45140fc 1c8088c0`<br>`c99df292 bad5b55b`<br>`c908e496 70ddb0f5`<br>`e012c886 e2b2af05`<br>`0ad0ae8b fb82db4a`|


