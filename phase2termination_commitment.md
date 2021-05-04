# Hermez Network Phase2 ceremony finalization (4th bootstrap)

During the last weeks we have been running the phase2 of the Hermez Network trusted
setup ceremony (4th bootstrap).

This phase2 was run on 3 circuits:
* circuit_2048_32_256_64_hez4: Batch processor of 2048 Txs.
* circuit_400_32_256_64_hez4: Batch processor of 400 Txs.
* withdraw_hez4: Circuit used to withdraw funds.

This ceremony was run by 6 trusted individuals from the community:

* [jordi](https://keybase.io/jbaylina)
* [eduadiez](https://keybase.io/eduadiez)
* [weijie](https://keybase.io/contactkohweijie)
* [jarradhope](https://keybase.io/jarradhope)
* [kobigurk](https://keybase.io/kobigurk)
* [jaszmin](https://keybase.io/jaszmin)

After the contributions, the b2sum of the zkey files resulting from the last contributions are:

#### b2sum circuit_2048_32_256_64_hez4_0006.zkey
````
77f74521 4607a805 d1720706 2822bd2d
c7c6a673 52c12163 ba5da75c 5e0341bc
5fd7b9d8 71834c80 cdc2ecfb 5cc86049
05576f99 ffff26f9 8703f08c c8d87d3d
````

#### circuit_400_32_256_64_hez3_0007.zkey
````
c86842c3 0980b11b 0316d60e b1825910
7251f978 24a23270 11cf058d af67c508
9314ffb3 1e26ad06 8e62a873 ce1ffa3c
f8f47b09 8f80d619 8c5d3d58 e864db95
````

#### withdraw_hez3_0007.zkey
````
7499f5a4 736b5f99 50232528 e962d3cc
429ccd3e 9809438d f9a8881e a2baf5b8
5a43e2e3 3dd26afe 53e01b85 c32fc761
2278d317 b8b8a8f1 0a2c6dfd 1dccbc94
````

You can see the details of the ceremony [here](https://github.com/hermeznetwork/phase2ceremony_4).

Before calculating the final zkey file, we will apply a random beacon to the three circuits.

Notice that according to [this](https://electriccoin.co/blog/reinforcing-the-security-of-the-sapling-mpc/), a random beacon might not be strictly necessary. Nevertheless, we consider it best practise to do so.

For this, we will apply the result of the round 824700 of [drand](https://drand.love).

Here is the info of the drand chain that will be used:

```
{
    "public_key":"868f005eb8e6e4ca0a47c8a77ceaa5309a47978a7c71bc5cce96366b5d7a569937c529eeda66c7293784a9402801af31",
    "period":30,
    "genesis_time":1595431050,
    "hash":"8990e7a9aaed2ffed73dbd7092123d6f289930540d7651336225dc172e51b2ce",
    "groupHash":"176f93498eac9ca337150b46d21dd58673ea4e3581185f869672e59fa4cb390a"
}
```

This number is planned to be generated on

Wednesday May 4th 2021, 23:47:00 UTC

Once the number is generated, one should be able to find it here:

[https://drand.cloudflare.com/public/824700](https://drand.cloudflare.com/public/824700)

or here:

[https://www.cloudflare.com/leagueofentropy](https://www.cloudflare.com/leagueofentropy)

In order to check the correctness of the software generating the number, check here:

[https://drand.love](https://drand.love)

You can also use this code to verify it:

[https://github.com/hermeznetwork/drand_verify](https://github.com/hermeznetwork/drand_verify)

Commit used: [https://github.com/hermeznetwork/drand_verify/commit/5047a94cc5786aecb98c56f0fecb37bed5440c01](https://github.com/hermeznetwork/drand_verify/commit/5047a94cc5786aecb98c56f0fecb37bed5440c01)

The `randomness` result (In Hexadecimal) will be treated as the `beaconHash`  used as the key generation beacon with 2^10 sha256 iterations.


Finally, the last contribution will be generated this way:

With snarkjs@0.3.60:

Commit: [https://github.com/iden3/snarkjs/commit/b335c01e988de31d59d71813a4137754711c8c85](https://github.com/iden3/snarkjs/commit/b335c01e988de31d59d71813a4137754711c8c85)

````bash
snarkjs zkey beacon circuit_2048_32_256_60_hez4_0006.zkey circuit_2048_32_256_64_hez4_final.zkey [beaconHash] 10
snarkjs zkey beacon circuit_400_32_256_60_hez4_0006.zkey circuit_400_32_256_60_hez4_final.zkey [beaconHash] 10
snarkjs zkey beacon withdraw_hez4_0006.zkey withdraw_hez4_final.zkey [beaconHash] 10
````


After this, we will generate the 3 solidity verification contracts this way:
````bash
snarkjs zkey export solidityverifier circuit_2048_32_256_60_hez4_final.zkey verifier2048.sol
snarkjs zkey export solidityverifier circuit_400_32_256_60_hez4_final.zkey verifier400.sol
snarkjs zkey export solidityverifier withdraw_hez4_final.zkey verifier_withdraw.sol
````
