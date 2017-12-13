<img src="https://github.com/kevinthomasbradley/cold-helium/blob/master/styles/logo1.png" width="150">

# Helium Crypto Coin [cold-helium]

## Description

This repository will contain an implementation of a cryptocurrency based on the litecoin / bitcoin source.

## Instructions

### Platform
* ubuntu-14.04.5-desktop-amd64
* litecoin-0.8.7.4

### Install Steps
1. sudo apt-get update
2. sudo apt-get install build-essential libtool autotools-dev autoconf pkg-config libssl-dev
3. sudo apt-get install libboost-all-dev
4. sudo add-apt-repository ppa:bitcoin/bitcoin
5. sudo apt-get update
6. sudo apt-get install libminiupnpc-dev

7. sudo apt-get install libdb4.8-dev
8. sudo apt-get install libdb4.8++-dev
9. sudo apt-get install libboost1.37-dev
(If using Boost 1.37, append -mt to the boost libraries in the makefile)

10. mv litecoin-0.8.7.4 heliumcoin
11. cd heliumcoin

### Renaming
1. find ./ -type f -readable -writable -exec sed -i "s/Litecoin/Heliumcoin/g" {} \;
2. find ./ -type f -readable -writable -exec sed -i "s/LiteCoin/HeliumCoin/g" {} \;
3. find ./ -type f -readable -writable -exec sed -i "s/LTC/HLC/g" {} \;
4. find ./ -type f -readable -writable -exec sed -i "s/litecoin/heliumcoin/g" {} \;
5. find ./ -type f -readable -writable -exec sed -i "s/litecoind/heliumcoind/g" {} \;

### Genesis Block Clearing
1. cd src
2. sudo nano main.cpp

3. empty the hashGenesisBlock in loadblockIndex function setting it to hashGenesisBlock = unint256("0x");
4. change the pchMessageStart[0] = 0xf3;
5. change the pchMessageStart[1] = 0xd2;
6. change txNew.vout[0].scriptPubKey first four values to 2809
6. update the pszTimestamp description to a relevant title for the day
8. below the txNew.vout[0].scriptPubKey there are two block.nNonce value, set both to 0;
9. set the epoc time to the current epoc time for instance 1513090749
11. delete our merkleRoute by setting block.hasMerkleRoot == unint256("0x") as we will generate a new one based on the epoc time and the timestamp
12. ensure all hashGenesisBlock are wiped to 0x (should only be two places)
13. change the block times by changing nTargetSpacing to 30 seconds and change the retarget aswell to 5 minutes by setting nTargetTimespan
14. get rid of checkpoints for litecoin in the src/checkpoints.cpp file
15. now modify the DNS seed list where the compiled initial wallet will exist by updating net.cpp strMainNetDNSSeed object to kevinthomasbradley.com and the IP address of my server (to obtain)
16. change the bitcoin-qt.pro to heliumcoin-qt.pro by cd .. then mv bitcoin-qt.pro heliumcoin-qt.pro
17. make fresh merkleRoot in the main.cpp by adding the following code after assert(block.hashMerkleRoot == uint256("0x"))
```
  // Code to create new MerkleRoot if not exists
  if (true && block.GetHash() != hashGenesisBlock)
  {
    printf("Searching for genesis block...\n");
    // This will figure out a valid hash and Nonce if you're
    // creating a different genesis block:
    uint256 hashTarget = CBigNum().SetCompact(block.nBits).getuint256();
    uint256 thash;
    char scratchpad[SCRYPT_SCRATCHPAD_SIZE];

    loop
    {
        scrypt_1024_1_1_256_sp(BEGIN(block.nVersion), BEGIN(thash), scratchpad);
        if (thash <= hashTarget)
            break;
        if ((block.nNonce & 0xFFF) == 0)
        {
            printf("nonce %08X: hash = %s (target = %s)\n", block.nNonce, thash.ToString().c_str(), hashTarget.ToString().c_str());
        }
        ++block.nNonce;
        if (block.nNonce == 0)
        {
            printf("NONCE WRAPPED, incrementing time\n");
            ++block.nTime;
        }
    }
    printf("block.nTime = %u \n", block.nTime);
    printf("block.nNonce = %u \n", block.nNonce);
    printf("block.GetHash = %s\n", block.GetHash().ToString().c_str());
  }
```
18. Copy the folder to create a clean source folder before compiling to upload to github

### First Compilation
1. compile by running from within the src folder make -f makefile.unix USE_UPNP=-
2. try running for the first time, we should get an exception stating assertion has failed due to an empty merkle root
3. this process created a hidden file so go to root directory by entering cd only no .. and doing ls -al and see the directory .heliumcoin
4. cd .heliumcoin/ then open the debug.log to acquire the new merkleRoot which is the last row, copy this merkleroot value
5. go back to the dirty source code i.e. the one not copied for githubs sake and open src/main.cpp searching for the hashMerkleRoot
variable and set this with the merkleroot obtained in step 4. in the debug print section
6. now we need to recompile the solution with make -f makefile.unix USE_UPNP=-
7. try to run again using ./heliumcoind, what it will do now is hash the genesis block (it is mining that first block)
8. in the debug.log again we can see the hashses it is trying to mine for the genesis block - can take 45~ minutes of so. Expected result will be an assertion fail
9. in the debug.log we will be able to now get our Nonnce (block.nNonce) and the Hash of our genesis block (block.GetHash)
10. so now that we have acquired the values we need we can update our 'clean' source - the one copied for githubs sake in step 18.
11. in the src/checkpoints.cpp add the genesis to our checkpoints in the mapCheckpoints instantiation
12. now we need to put it in main.cpp replacing the 0x with 0x<VALUE> in two places
13. also replace the nonces (block.nNonce) of value 0 with the <VALUE> obtained in 9. in two places also
14. now we need to modify the graphics for our heliumcoin in src/qt/res/icons modifying primarily the bitcoin.ico and bitcoin.png files to the helium logo
15. modify the wallet banner image in src/qt/res/images about.png and splash.png
16. remove the old .heliumcoin directory by running sudo rm -rf .heliumcoin/ then recompile the solution in src with make -f makefile.unix USE_UPNP=-
17. create a config file at .heliumcoin/heliumcoin.conf file with the following settings rpcuser=heliumcoinrpc and rpcpassword=2fFfwFUwjiGV9684b17vk1B2Jpb9AxPZftZmNYkC8EQg
18. at this point simply test all is as expected by running with ./heliumcoind -daemon and then ./heliumcoind getinfo to verify all is as expected



## Documentation

Please refer to each sub folders README file for the necessary documentation of each layer.

## Author
* Kevin Thomas Bradley | kevin.thomas.bradley@gmail.com
