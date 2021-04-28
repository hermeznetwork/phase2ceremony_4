# Hermez Phase 2 ceremony attestation

**Contribution Number:** 0003

**Name:** Koh Wei Jie

**Date:** April 26 - 28 2021

**Location:** The cloud

**Device:** Google Cloud VM n2d-standard-8 (8 vCPUs, 32 GB memory)
Confidential VM service enabled

I used a Google Cloud account enrolled in [Google's Advanced Protection
Program](https://landing.google.com/advancedprotection/). The Confidential
Computing option purports to provide encryption to data in the cloud while it
is being processed. I can't speak to whether this service is truly secure, but
generally, the more diverse the hardware/VMs used, the more secure the
ceremony. As such, even if Confidential Computing is only as secure than a
regular Google Cloud VM, an adversary will have the already difficult task of
compromising Google Cloud in addition to compromising every other participant's
device.

**Entropy sources:** Hex values from [the Australian
National University's Quantum Random Number
Generator](https://qrng.anu.edu.au/random-hex/) and the output of `python3 -c
'import secrets; print(secrets.token_hex(40960))'` from my laptop.

I downloaded the following files and checked that their Blake2 hashes match
those attested by the previous participant.

```
d3c1dc1fca95879b2abaad05e90ca3f2382f3b3f9ca45ca131f72907b6da49ca988816da4f2197535abf6285ddfd3dbbed387ca843c8d3f38873cd2442d46e1b *circuit-2048-32-256-64_hez4_0002.zkey
4decdf2203a63d61cb483be6d71d91b93dee27dc71873ad7900ee9d2160c1929658d641933466b550366d5b6207ec4d3c1dd55edff1617b3999e35223aeceb69 *circuit-400-32-256-64_hez4_0002.zkey
396141de47a951a27c506da6467aae325abdfa155cf7a501a04c4a45fe5fd948f986ddff5acd32e07d43fd6986b476d6947c0616417a145146e87da463cb72e5 *withdraw_hez4_0002.zkey
```

## My contributions

The Blake2 checksums of my contribution files are:

```
73d5c2dd970f435e1605bf2dccd7357dacaa857f7afb98f1673344c2a160fbc91c9e7838a99add9eeda3e61711842eddf50d05b8cce07cf2776fd839348cf469 *withdraw_hez4_0003.zkey
a1487f791b261806cad24326bc13a3766d321a886dffaa1db5e762a10cad51264a91be8ef99718d4b5b73645c3405f7fdbf1af821820f368126c7c7a99c9fc50 *circuit-400-32-256-64_hez4_0003.zkey
e6a471b455e4653a438af926bc66ee46f90ac2bcc124278d53fd3fb7ad798635b9469545eae4c1ed949bd5096c9b0f4fc338bced6146770c0c6ca5bf49cc3e87 *circuit-2048-32-256-64_hez4_0003.zkey
```

The circuit and contribution hashes from `snarkjs` are:

```
snarkjs zkey contribute withdraw_hez4_0002.zkey withdraw_hez4_0003.zkey -v -n=weijie
[INFO]  snarkJS: Circuit Hash: 
                26f8883b 44e999e7 dc749bac d7a914e3
                4fe4602e bb18d681 39a70cd9 16c22722
                2ec186f5 1dd876d1 04a403df 13433b27
                7c0c69a3 3a2aca64 dc3365c0 ce74a0ec
[INFO]  snarkJS: Contribution Hash: 
                c0524bc4 7734880f 44a68679 b0ec4d83
                cf67fcc1 db6636bf 9635914b 3a4dbbf9
                aa6add4e c2a892e8 f6e3db90 cc5e1eb1
                b76353d0 68bd0b00 708bbda8 15b730cd
```

```
snarkjs zkey contribute circuit-400-32-256-64_hez4_0002.zkey circuit-400-32-256-64_hez4_0003.zkey
[INFO]  snarkJS: Circuit Hash: 
                c1ca41db 5e0efe0f 4b379819 7150bf1d
                7c12c3a5 0a14d395 6076ca0f 951f88e0
                48798727 ca0dc850 394be4ff 5e555830
                16ffb04b 41ef718c b83b76c3 4456d8d7
[INFO]  snarkJS: Contribution Hash: 
                69106a39 02378454 19543bd9 35a3fdc8
                adeb625b e94ef09c 829d0519 f1195df7
                32e67d93 66cd72d2 b80e6b48 57338f9f
                fab84dcd 3c398685 2991c25d 976fc75c
```

```
snarkjs zkey contribute circuit-2048-32-256-64_hez4_0002.zkey circuit-2048-32-256-64_hez4_0003.zkey -v -n=weijie
[INFO]  snarkJS: Circuit Hash: 
                ab79af62 13888d71 8e5748fb 5eb7a0c9
                76d45799 29f12c7e 207f0193 65936ed8
                45154b61 86e657f9 1785c5a6 820986b9
                6b2d296f 15fadbb4 05d2b67c 2a875f63
[INFO]  snarkJS: Contribution Hash: 
                0b4dddbb f5b7c9f0 2c6667ab 5d8887a4
                560cf20f ae70476d 026fdefb 77792da6
                cdb0249a b10169fd 67459811 1072bacc
                09e6ba64 a5697faf dc6ba9df 18c7836b
```
