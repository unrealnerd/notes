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

### Foundation app analysis

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
