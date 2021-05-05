# Verification of the ceremony guide

In order to verify the circuit, you will need a machine with tons of memory.

For reference, the one we are using is a machine with 16cores and 512Gb of RAM.

In this tutorial we will give instructions for a x1e.8xlarge aws instance. This instance has 16 cores 32 threads, 976Gb of RAM and it's important to build it with a SSD of 960Gb that we will use as swap space. The instance will use Ubuntu 20.04 and the cost of the instance is 3.336$/h.

So lets start by launching and instece. Be sure to have 1023GB of EBS and local SSD storage.

## Basic OS preparation

````
sudo apt-get install tmux
````

## Adding swap and tweeking the OS to accept high amount of memory.

````
sudo mkswap -f /dev/xvdb
sudo swapon /dev/xvdb

sudo fallocate -l 400G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

sudo sh -c 'echo "vm.max_map_count=10000000" >>/etc/sysctl.conf'
sudo sh -c 'echo 10000000 > /proc/sys/vm/max_map_count'
````

It's important to note that we need 2200G of memory adding RAM and swap together. That's why we use the full local disk plus a 400G file.

## Compile a patched version of node

First install node with nvm:

````
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
source ~/.bashrc
nvm install v14.8.0
node --version
````

Now compile a patched version of node.  This aptch avoids activating garbage collections in memory presure situations and many threads.  This is very convininet for the optimization program.

````
sudo apt-get update
sudo apt-get install python3 g++ make
git clone git clone https://github.com/nodejs/node.git
cd node
git checkout 8beef5eeb82425b13d447b50beafb04ece7f91b1
patch -p1 <<EOL
index 0097683120..d35fd6e68d 100644
--- a/deps/v8/src/api/api.cc
+++ b/deps/v8/src/api/api.cc
@@ -7986,7 +7986,7 @@ void BigInt::ToWordsArray(int* sign_bit, int* word_count,
 void Isolate::ReportExternalAllocationLimitReached() {
   i::Heap* heap = reinterpret_cast<i::Isolate*>(this)->heap();
   if (heap->gc_state() != i::Heap::NOT_IN_GC) return;
-  heap->ReportExternalMemoryPressure();
+  // heap->ReportExternalMemoryPressure();
 }

 HeapProfiler* Isolate::GetHeapProfiler() {
diff --git a/deps/v8/src/objects/backing-store.cc b/deps/v8/src/objects/backing-store.cc
index bd9f39b7d3..c7d7e58ef3 100644
--- a/deps/v8/src/objects/backing-store.cc
+++ b/deps/v8/src/objects/backing-store.cc
@@ -34,7 +34,7 @@ constexpr bool kUseGuardRegions = false;
 // address space limits needs to be smaller.
 constexpr size_t kAddressSpaceLimit = 0x8000000000L;  // 512 GiB
 #elif V8_TARGET_ARCH_64_BIT
-constexpr size_t kAddressSpaceLimit = 0x10100000000L;  // 1 TiB + 4 GiB
+constexpr size_t kAddressSpaceLimit = 0x40100000000L;  // 4 TiB + 4 GiB
 #else
 constexpr size_t kAddressSpaceLimit = 0xC0000000;  // 3 GiB
 #endif
EOL
./configure
make -j16
````

This will compile a patched version of node.

## Download and prepare circom

````
cd ~
git clone https://github.com/iden3/circom.git
cd circom
git checkout v0.5.44
npm install
git log
````

The hash of the commit should be: c311ae355239bae76a83b92070cb21d249e77465


## Prepare the circuits

````
cd ~
git clone https://github.com/hermeznetwork/circuits.git
git checkout v1.0.0
cd circuits
npm install
cd tools
node build-circuit.js create 2048 32 256 64
node build-circuit.js create 400 32 256 64
mkdir withdraw
cd withdraw
cat >withdraw.circom <<EOL
include "../../src/withdraw.circom";

component main = Withdraw(32);
EOL
````

The hash of the v1.0.0 tag should be: `45a486464334e08bd5da948ffb2099fcf1dc7c2f`

## Compile the circuits

The compilation may take a while. You may want to run this step in `tmux` session.

##### 2048 Txs Circuit
````
cd ~/circuits/tools/rollup-1912-32-256-64
node --trace-gc --trace-gc-ignore-scavenger --max-old-space-size=2048000 --initial-old-space-size=2048000 --no-global-gc-scheduling --no-incremental-marking --max-semi-space-size=1024 --initial-heap-size=2048000 ../../../circom/cli.js circuit-2048-32-256-64.circom -f -r -v -n "^RollupTx$|^DecodeTx$|^FeeTx$|^Sha256compression$"
````
##### 400Txs Circtuit
````
cd ~/circuits/tools/rollup-344-32-256-64
node --trace-gc --trace-gc-ignore-scavenger --max-old-space-size=2048000 --initial-old-space-size=2048000 --no-global-gc-scheduling --no-incremental-marking --max-semi-space-size=1024 --initial-heap-size=2048000 ../../../circom/cli.js circuit-400-32-256-64.circom -f -r -v -n "^RollupTx$|^DecodeTx$|^FeeTx$|^Sha256compression$"
````

##### Withdraw Circtuit
````
cd ~/circuits/tools/withdraw
node ../../../circom/cli.js -r -v withdraw.circom
````

As you can see the withdraw circuit is run without -f so circom will aready optimize it.

## Reaname and check the resulting files:

##### 2048Txs Circuit
````
cd ~/circuits/tools/rollup-2048-32-256-64
mv circuit-2048-32-256-64.r1cs circuit-1912-32-256-64_no.r1cs
b2sum circuit-2048-32-256-64_no.r1cs
````

The result should be:

````
a66dfa02 86e18bed 1d6d8504 27e2ce70
6a2ce7c4 1d7e20fb 8b3cbfee b03e15d6
7850c912 9a8db694 1dc5a4ba 23df0a62
7d9fb64f 6af7d66e 43eda445 44955e98
````

##### 400Txs Circtuit
````
cd ~/circuits/tools/rollup-400-32-256-64
mv circuit-400-32-256-64.r1cs circuit-400-32-256-64_no.r1cs
b2sum circuit-400-32-256-64_no.r1cs
````

The result should be:

````
fe9a76b1 acad587f 774300bd 17639067
48cb7a03 4e5849d8 753f4cd7 f4560467
2373951e 0c78723e efa6c8b9 e28d51ba
24e9aec6 bf402306 677b7e9c 875fff60
````

##### Withdraw Circtuit

````
cd ~/circuits/tools/withdraw
b2sum withdraw.r1cs
````

The result should be:

````
848c27e0 fbd1d84b 0d5ce04f f50f2090
2b27ac00 f596b332 1df9acae 8b71ee2b
8d46cff5 ad6da39a 2d893a2e 852e2a2d
b8a49f03 fd3a1b74 11d050c0 718643d8
````

## Optimize the circuits

````
cd ~
git clone https://github.com/iden3/r1csoptimize
cd r1csoptimize
git checkout 92a0ae3569cedeaf6caa8176afc0a74f7c9a0e03
npm install
`````

##### 2048 Txs circuit
````
~/node/out/Release/node --trace-gc --trace-gc-ignore-scavenger --max-old-space-size=2048000 --initial-old-space-size=2048000 --no-global-gc-scheduling --no-incremental-marking --max-semi-space-size=1024 --initial-heap-size=2048000 --expose-gc src/cli_optimize.js ../circuits/tools/rollup-2048-32-256-64/circuit-2048-32-256-64_no.r1cs ../circuits/tools/rollup-2048-32-256-64/circuit-2048-32-256-64.r1cs
````
And then check the hash of the result.
````
b2sum ../circuits/tools/rollup-1912-32-256-64/circuit-2048-32-256-64.r1cs
````

this should be:

````
30991e28 43336019 493c63c8 b4a7e2b8
f633b111 64a6703d 75d28d2f 5e955c62
4cd57eb5 da8fbec9 f46c8379 b5b7290e
954df188 aefb8957 bbd43499 37ba8e07
````



##### 400 Txs circuit
````
~/node/out/Release/node --trace-gc --trace-gc-ignore-scavenger --max-old-space-size=2048000 --initial-old-space-size=2048000 --no-global-gc-scheduling --no-incremental-marking --max-semi-space-size=1024 --initial-heap-size=2048000 --expose-gc src/cli_optimize.js ../circuits/tools/rollup-400-32-256-64/circuit-400-32-256-64_no.r1cs ../circuits/tools/rollup-400-32-256-64/circuit-400-32-256-64.r1cs
````
And then check the hash of the result.
````
b2sum ../circuits/tools/rollup-400-32-256-64/circuit-400-32-256-64.r1cs
````

this should be:

````
f5b06e6b bbd6df96 bbfdf710 303577f2
524f3b7e 9bf1c0a8 65faff88 1773dd53
1f19766c 704bc256 e37a0fb5 d330cf04
60453250 9b98e633 964e7219 08e66721
````

## install snarkjs

````bash
cd ~
git clone https://github.com/iden3/snarkjs.git
cd snarkjs
git checkout v0.3.59
npm install
````

## Downloading and verifying powers of Tau

First you will need to download the full powersOfTau file:

````bash
cd ~
wget https://hermez.s3-eu-west-1.amazonaws.com/powersOfTau28_hez_final.ptau
````

Check the blake2b hash of the circuit:
````bash
b2sum powersOfTau28_hez_final.ptau
````

The result should be:

````
55c77ce8 562366c9 1e7cda39 4cf7b7c1
5a06c12d 8c905e8b 36ba9cf5 e13eb37d
1a429c58 9e8eaba4 c591bc4b 88a0e282
8745a53e 170eac30 0236f5c1 a326f41a
````

If you are also interested in verifying the powers of tau, you can check [this block post](https://blog.hermez.io/zero-knowledge-proofing-hermez-a-quick-guide-to-our-cryptographic-setup/) and [this other](https://blog.hermez.io/zero-knowledge-proofing-hermez-preparephase2-update/)

If you dont trust, the powers of tau, you can verify the powers of tau file with this command:
````bash
~/node/out/Release/node --trace-gc --trace-gc-ignore-scavenger --max-old-space-size=2048000 --initial-old-space-size=2048000 --no-global-gc-scheduling --no-incremental-marking --max-semi-space-size=1024 --initial-heap-size=2048000 --expose-gc ../../../snarkjs/cli.js ptv powersOfTau28_hez_final.ptau -v >verification-pot.txt
````

At the end of the verification-pot.txt you can see the 55 contributions. You now should match the responses showed here with the attestations of the [perpetual powers of tau transcript](https://github.com/weijiekoh/perpetualpowersoftau).

You can see also the random beacon:

````
e586fcca f245c9a1 d7e78294 d4802018
f3001149 a71b8f10 cd997ef8 235aa372
````

This number is the drand round 100,000 which you can find [here](https://drand.cloudflare.com/public/100000)

The procedure was anaunced [here](https://etherscan.io/tx/0x98e38b88ed244a73ce7c1e3ab4f37439ae1faead555a20fd376496339adc2fed): on august 24th, 2020
And the beacon random wos generated on august 26th, 2020

To see that it was calculated that date,
You can see the genesis drand [here](https://drand.cloudflare.com/info)

The unix timestamp of the drand genesis time is: 1595431050
The period is: 30
So the generation time is: 1595431050 + 100000*30 = 1598431050
If we convert this unix time in seconds to readable time, it is Wednesday, 26 August 2020 08:37:30

#### Verify the zkey file.

##### 2048Tx circuit
````
~/node/out/Release/node --trace-gc --trace-gc-ignore-scavenger --max-old-space-size=2048000 --initial-old-space-size=2048000 --no-global-gc-scheduling --no-incremental-marking --max-semi-space-size=1024 --initial-heap-size=2048000 --expose-gc ../../../snarkjs/cli.js zkv circuit-2048-32-256-64.r1cs ../../../powersOfTau28_hez_final.ptau circuit-2048-32-256-64_hez4_final.zkey -v >verification-2048.txt
````

The circuit hash must be:
````
ab79af62 13888d71 8e5748fb 5eb7a0c9
76d45799 29f12c7e 207f0193 65936ed8
45154b61 86e657f9 1785c5a6 820986b9
6b2d296f 15fadbb4 05d2b67c 2a875f63
````

If you want to check an intermediary circuit, Substitute _final by the intermediary response you want.

You can check all the contributions at the end of the `verification-2048.txt` file. You should check all the signatures of the transcript and see that the declared contribution hash of each particimat matches the ones here.

As far as there is a single attestatation you trust, you can considere the ceremony for this circuit safe.

##### 400 Tx circuit
````
~/node/out/Release/node --trace-gc --trace-gc-ignore-scavenger --max-old-space-size=2048000 --initial-old-space-size=2048000 --no-global-gc-scheduling --no-incremental-marking --max-semi-space-size=1024 --initial-heap-size=2048000 --expose-gc ../../../snarkjs/cli.js zkv circuit-400-32-256-64.r1cs ../../../powersOfTau28_hez_final.ptau circuit-400-32-256-64_hez4_final.zkey -v >verification-400.txt
````

The hash of the circuit must be:
````
c1ca41db 5e0efe0f 4b379819 7150bf1d
7c12c3a5 0a14d395 6076ca0f 951f88e0
48798727 ca0dc850 394be4ff 5e555830
16ffb04b 41ef718c b83b76c3 4456d8d7
````

You have to match also the contribution hashes of the participants with attestations they published.

As far as there is a single attestatation you trust, you can considere the ceremony for this circuit safe.

##### Withdraw  circuit
````
~/node/out/Release/node --trace-gc --trace-gc-ignore-scavenger --max-old-space-size=2048000 --initial-old-space-size=2048000 --no-global-gc-scheduling --no-incremental-marking --max-semi-space-size=1024 --initial-heap-size=2048000 --expose-gc ../../../snarkjs/cli.js zkv withdraw.r1cs ../../../powersOfTau28_hez_final.ptau withdraw_hez4_final.zkey -v >verification-withdraw.txt
````

The hash of the circuit must be:
````
26f8883b 44e999e7 dc749bac d7a914e3
4fe4602e bb18d681 39a70cd9 16c22722
2ec186f5 1dd876d1 04a403df 13433b27
7c0c69a3 3a2aca64 dc3365c0 ce74a0ec
````

You have to match also the contribution hashes of the participants with attestations they published.

As far as there is a single attestatation you trust, you can considere the ceremony for this circuit safe.

#### Phase2 Random beacon verification

You can see also the random beacon of the three circuits is:

````
c62e6422dd91eeac7d2e0ad2af0fd904b73bb3095d8756f88a7a01bda97e639e
````

This number is the drand round 824700 which you can find [here](https://drand.cloudflare.com/public/824700)

The procedure was anaunced [here](https://etherscan.io/tx/0xb8534a5adb2c6e81623ad77b18e9c1107594f65290ee199549f786b7c368de27): on May 4th, 2021 21:37 UTC
And the beacon random wos generated on May 4th, 2021 23:47 UTC

To see that it was calculated that date,
You can see the genesis drand [here](https://drand.cloudflare.com/info)

The unix timestamp of the drand genesis time is: 1595431050
The period is: 30
So the generation time is: 1595431050 + 824700*30 = 1620172050
If we convert this unix time in seconds to readable time, it is May 4th, 2021 23:47:30 UTC

#### Make a public post.

If you reach this point, it would be great if you can publish a public post or at least a tweet explaining that you verified the circuits.

For any incident you find, please do not hesitate to contact us in the Hermez discord channel or in [ the telegram group](https://t.me/hermez_network)



