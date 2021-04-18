# Hermez Network Phase2 ceremony finalization

During the last weeks we have been running the phase2 of the Hermez Network trusted
setup ceremony.

This phase2 was run on 3 circuits:
* circuit_1912_32_256_64: Batch processor of 1912 Txs.
* circuit_344_32_256_64: Batch processor of 344 Txs.
* withdraw: Circuit used to withdraw funds.

This ceremony was run by 7 trusted individuals from the community:

* [jordi](https://keybase.io/jbaylina)
* [kobigurk](https://keybase.io/kobigurk)
* [eduadiez](https://keybase.io/eduadiez)
* [thore1234](https://keybase.io/thore1234)
* [josephc](https://keybase.io/cdili)
* [weijie](https://keybase.io/contactkohweijie)
* [jarradhope](https://keybase.io/jarradhope)

After the contributions, the b2sum of the zkey files resulting from the last contributions are:

#### b2sum circuit_1912_32_256_64_hez3_0007.zkey
````
3aceeb98 42ecc5d6 ff0ce9be 184f5b2e
21182fbb e1ba642b f751d799 59d16a95
D996f0d0 4e91b0d9 7606ccdf 557bfb37
0263a05f 48d40932 d45f8b77 3a0e68e0
````

#### circuit_344_32_256_64_hez3_0007.zkey
````
63fcde7c 5b5a2ee2 abaec90f a02025ad
Eb6559bd 5b318593 b4cec080 daf51996
54ae6f00 0af6cd7e 9393d3a9 0e1a5bfb
3e1d52b7 2a065d9d 65eb8e60 57dbf886
````

#### withdraw_hez3_0007.zkey
````
072543ae 5b51b2e7 4ea1ce56 a8afde52
B58ad938 06d595ab 8512880f bb5aa592
Bf765ec5 e2e58948 6d23bd5c D4ca9f88
7eacb995 51779402 c46f25f8 9b506184
````

You can see the details of the ceremony [here](https://github.com/hermeznetwork/phase2ceremony_3).

Before calculating the final zkey file, we will apply a random beacon to the three circuits.

Notice that according to [this](https://electriccoin.co/blog/reinforcing-the-security-of-the-sapling-mpc/), a random beacon might not be strictly necessary. Nevertheless, we consider it best practise to do so.

For this, we will apply the result of the round 697500 of [drand](https://drand.love).

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

Sunday March 21st, 20:37:00 UTC

Once the number is generated, one should be able to find it here:

[https://drand.cloudflare.com/public/697500](https://drand.cloudflare.com/public/100000)

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
snarkjs zkey beacon circuit_1912_32_256_60_hez3_0007.zkey circuit_1912_32_256_60_hez3_final.zkey [beaconHash] 10
snarkjs zkey beacon circuit_344_32_256_60_hez3_0007.zkey circuit_1912_32_256_60_hez3_final.zkey [beaconHash] 10
snarkjs zkey beacon withdraw_hez3_0007.zkey circuit_1912_32_256_60_hez3_final.zkey [beaconHash] 10
````


After this, we will generate the 3 solidity verification contracts this way:
````bash
snarkjs zkey export solidityverifier circuit_1912_32_256_60_hez3_final.zkey verifier1912.sol
snarkjs zkey export solidityverifier circuit_344_32_256_60_hez3_final.zkey verifier344.sol
snarkjs zkey export solidityverifier withdraw_hez3_final.zkey verifier_withdraw.sol
````
