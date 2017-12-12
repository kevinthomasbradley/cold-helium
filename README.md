<img src="https://github.com/kevinthomasbradley/cold-helium/blob/master/styles/logo.png" width="200"> 

# Helium Crypto Coin [cold-helium]

## Description

This repository will contain an implementation of a cryptocurrency based on the litecoin / bitcoin source.

## Instructions

# Platform
ubuntu-14.04.5-desktop-amd64
litecoin-0.8.7.4

# Steps
sudo apt-get update
sudo apt-get install build-essential libtool autotools-dev autoconf pkg-config libssl-dev
sudo apt-get install libboost-all-dev
sudo add-apt-repository ppa:bitcoin/bitcoin
sudo apt-get update
sudo apt-get install libminiupnpc-dev 

sudo apt-get install libdb4.8-dev
sudo apt-get install libdb4.8++-dev
sudo apt-get install libboost1.37-dev
(If using Boost 1.37, append -mt to the boost libraries in the makefile)

mv litecoin-0.8.7.4 heliumcoin
cd heliumcoin

find ./ -type f -readable -writable -exec sed -i "s/Litecoin/Heliumcoin/g" {} \;
find ./ -type f -readable -writable -exec sed -i "s/LiteCoin/HeliumCoin/g" {} \;
find ./ -type f -readable -writable -exec sed -i "s/LTC/HLC/g" {} \;
find ./ -type f -readable -writable -exec sed -i "s/litecoin/heliumcoin/g" {} \;
find ./ -type f -readable -writable -exec sed -i "s/litecoind/heliumcoind/g" {} \;

cd src
sudo nano main.cpp

1. empty the hashGenesisBlock in loadblockIndex function setting it to hashGenesisBlock = unint256("0x");
2. change the pchMessageStart[0] = 0xf3;
3. change the pchMessageStart[1] = 0xd2;
4. change txNew.vout[0].scriptPubKey first four values to 2809
5. update the pszTimestamp description to a relevant title for the day
6. below the txNew.vout[0].scriptPubKey there are two block.nNonce value, set both to 0;
7. set the epoc time to the current epoc time for instance 1513090749
8. delete our merkleRoute by setting block.hasMerkleRoot == unint256("0x") as we will generate a new one based on the epoc time and the timestamp
9. ensure all hashGenesisBlock are wiped to 0x (should only be two places)
10. change the block times by changing nTargetSpacing to 30 seconds and change the retarget aswell to 5 minutes by setting nTargetTimespan
11. get rid of checkpoints for litecoin in the src/checkpoints.cpp file

## Documentation

Please refer to each sub folders README file for the necessary documentation of each layer.

## Author
* Kevin Thomas Bradley | kevin.thomas.bradley@gmail.com
