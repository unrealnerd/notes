# NFT Notes
---
### Learn

- [Solidity by example](https://solidity-by-example.org/)
- [Solidity docs](https://docs.soliditylang.org/)
- [adding BSC to MetaMask](https://academy.binance.com/en/articles/connecting-metamask-to-binance-smart-chain)
- [connecting metamask to bsc](https://dapp-world.com/blogs/01/how-to-connect-metamask-to-binance-smart-chainbsc--1618137142422)
- [BSC scan](https://testnet.bscscan.com/address/0xa8dC4fB8DB32A09499cA53dD75F7B6a74F49c05e#events)
- [BNB test network add token](https://testnet.binance.org/faucet-smart)
- [Wazirx NFT web ui](http://nft.wazirx.com/)
- [go client for ethereum using docker](https://geth.ethereum.org/docs/install-and-build/installing-geth#install-on-windows)
- [standards doc](https://docs.openzeppelin.com/contracts/2.x/tokens#ERC721)
- [to generate contract.sol](https://wizard.openzeppelin.com/)
- [calling function from another contract](https://www.youtube.com/watch?v=YxU87o4U5iw)
- [Reentrancy](https://blog.openzeppelin.com/reentrancy-after-istanbul/)
  - re-entering the contract when in invariant state
  - balance > amout --> transfer --> (re-enters) --> balance = balance - amt; actual amount is trasfered but the state of balance is not updated when re-entering.
  - one way to avoid is setting balance before making the actual call to transfer
  - second way is [nonReentrant modifier](https://docs.openzeppelin.com/contracts/2.x/api/utils#ReentrancyGuard)
- to compile solidity contract

    ```bash
        docker run -v ${pwd}:/sources ethereum/solc:stable -o /sources/contracts/output --abi --bin --overwrite /sources/contracts/WazirXNFT.sol --allow-paths /sources/node_modules
        
        docker run -v ${pwd}:/sources -it ethereum/client-go:alltools-stable
        
        abigen --abi=/sources/contracts/output/WazirXNFT.abi --pkg=connectors --out=/sources/contracts/output/WazirXNFT.go --bin /sources/contracts/output/WazirXNFT.bin
        
        go get
    ```

- `storage` vs `memory` - storage(harddisk) is default for state variables. memory(ram) is deafault for function arguments cleared between the calls
- 1 eth = 10^18 wei
- when you get this error `Error: Artifacts are from different compiler runs`
  - delete `.openzeppelin` and `build` folder
  - `npx truffle migrate --compile-all`
- connecting remix IDE to local `remixd -s /c/code/NFTMarketplace --remix-ide https://remix.ethereum.org/#optimize=false&runs=200&evmVersion=null&version=soljson-v0.8.4+commit.c7e474f2.js`
- step by step instructions to use [truffle](https://forum.openzeppelin.com/t/openzeppelin-upgrades-step-by-step-tutorial-for-truffle/3579)
- to skip migrations in localhost

```truffle-config.js
networks: {
    skipMigrations: {
      development: {
        host: "127.0.0.1",     // Localhost (default: none)
        port: 8545,            // Standard BSC port (default: none)
        network_id: "*",       // Any network (default: none)
      }
    },
    testnet: { 
    ...
```

- install ganache GUI on local

```bash
npm install --global windows-build-tools
git clone https://github.com/trufflesuite/ganache.git
npm install
npm start
```

- using docker

```bash
docker rm ganache-gui
docker run -d -p "7545:7545" --name ganache-gui ganache-gui 
```

### The graph

- [in depth](https://thegraph.com/blog/the-graph-network-in-depth-part-1)

### Foundation app analysis

- [UML](images/nft-uml.png)
- [Foundation martket contract](https://etherscan.io/address/0xcDA72070E455bb31C7690a170224Ce43623d0B6f#code)
- [Foundation token contract](https://etherscan.io/address/0x3B3ee1931Dc30C1957379FAc9aba94D1C48a5405#code)
- [Martket place code from proxy](https://etherscan.io/address/0x52e75b420e70ea24bbd0ede2e71800a913cd06ee#code)

- `FoundationTreasuryNode` treasury contract address set on intilaize and expose get method
- `FNDNFTMarket` - initalizes treasuary, auction and reservation
- `NFTMarketCreators` - gets creators address of the nft
- `NFTMarketCore` - gets the current owner of the nft
- `NFTMarketReserveAuction`
  - `createReserveAuction` creates a new auction for a token. internally increments auctionid and use it as pk
  - An NFT on auction is on escrow until finialized or cancelled
  - `_duration` / default 24 hours duration for which the NFT is on auction
  - `_minPercentIncrementInBasisPoints` and `_duration` is set in `ReserveAuctionConfig`
  - `_minPercentIncrementInBasisPoints` is the minimum percent by which the bid amount should be greater.
  - durataion must be greater than extenstion duration(15mins)
  - `reservePrice` minimum selling price.
  - `updateReserveAuction` seller is allowed to change reserveproce in case no bidders
  - `cancelReserveAuction` when seller cancels, release from escrow
  - `placeBid` 

### Jots
```
my account 0xF13462D8916996C4672CF05c0d0C515D61050B20
created token
approve token on wazirnft for escrow contract
approve auction market on escrow contract
note (bool is True not true)
place on auction
place bid form different account
```

WRX TOKEN : `0xe9b7fe48C641278F451230e4824fc52eBe84DE65`
WRX NFT: `0xd41F8c0fe77f7d2c68a3f5e501eB5B8DE76A195F`
Escrow: `0x5433378C2366CFDFd9DEF519aFd2a06d27A46e63`

ACCOUNT 1: `0xF13462D8916996C4672CF05c0d0C515D61050B20`
ACCOUNT 2: `0x4DEC5475389bA78895aD0e452b95664277B60337`
ACCOUNT 3: `0x801CA501cb248F6B4ECc8Df1e222443b198b4910`
ACCOUNT 4: `0xc39BD7496631F3FaD5a85AeA1F3D5798BB3b6393`

Initialize Auction Market
nft : `0xd41F8c0fe77f7d2c68a3f5e501eB5B8DE76A195F`
escrow: `0x5433378C2366CFDFd9DEF519aFd2a06d27A46e63`
bep20: `0xe9b7fe48C641278F451230e4824fc52eBe84DE65`
auctionDuration: `600`
auctionExtension: `300`
minBidIncrement: `1000000000`
decimals: `8`
serviceOwner: `0xc39BD7496631F3FaD5a85AeA1F3D5798BB3b6393`
serviceCommission: `500000000`

setContractApproval for AuctionMarket on Escrow
approve escrow on tokenID in nft contract
create auction
approve auction to spend on accout 2
place bid
approve auction to spend on accout 3
place bid


npx truffle migrate --compile-all --network development
npx truffle test --skip-dry-run