# NFT Notes

## ----------------

### existing code walkthorugh

- wallet address is mapped to email id & username
- update profie using walletaddress
- profille pic stored in s3
- web3service used for accessing NFT
- get all tokens using limit froma nd to params to show all tokens in the UI
- token hash json file ? ipfs? token meta data in a json file in cdn?
- whats CDN URL? ENV variable access?
- [adding BSC to MetaMask](https://academy.binance.com/en/articles/connecting-metamask-to-binance-smart-chain)
- [connecting metamask to bsc](https://dapp-world.com/blogs/01/how-to-connect-metamask-to-binance-smart-chainbsc--1618137142422)
- [BSC scan](https://testnet.bscscan.com/address/0xa8dC4fB8DB32A09499cA53dD75F7B6a74F49c05e#events)
- [BNB test network add token](https://testnet.binance.org/faucet-smart)
- [Wazirx NFT web ui](http://wazirxnftdevfrontend-env.eba-k5ubiwwu.ap-southeast-1.elasticbeanstalk.com/nft/50)
- [go client for ethereum using docker](https://geth.ethereum.org/docs/install-and-build/installing-geth#install-on-windows)
- [standards doc](https://docs.openzeppelin.com/contracts/2.x/tokens#ERC721)
- [to generate contract.sol](https://wizard.openzeppelin.com/)
- [Reentrancy](https://blog.openzeppelin.com/reentrancy-after-istanbul/)
- to compile solidity contract

```bash
    docker run -v ${pwd}:/sources ethereum/solc:stable -o /sources/contracts/output --abi --bin --overwrite /sources/contracts/WazirXNFT.sol --allow-paths /sources/node_modules
    
    docker run -v ${pwd}:/sources -it ethereum/client-go:alltools-stable
    
    abigen --abi=/sources/contracts/output/WazirXNFT.abi --pkg=connectors --out=/sources/contracts/output/WazirXNFT.go --bin /sources/contracts/output/WazirXNFT.bin
    
    go get
```

https://testnet.bscscan.com/address/0x6C2B386d71d6Da1ec65375601986EfaF5Ad0D2e2#events
https://remix.ethereum.org/
http://wazirxnftdevfrontend-env.eba-k5ubiwwu.ap-southeast-1.elasticbeanstalk.com/

setting up wsl

```bash
sudo apt install golang-go
sudo apt install gccgo-go
```

`.\geth.exe --ropsten --syncmode "light"` 
Public address of the key:   0x392F0E188e9D238cC2c3Ca8299eb431a5e3E1a43
Path of the secret key file: C:\Users\arjun_shetty\AppData\Local\Ethereum\keystore\UTC--2021-05-06T14-38-58.853541300Z--392f0e188e9d238cc2c3ca8299eb431a5e3e1a43

### Foundation app contracts

- [Foundation martket contract](https://etherscan.io/address/0xcDA72070E455bb31C7690a170224Ce43623d0B6f#code)
- [Foundation token contract](https://etherscan.io/address/0x3B3ee1931Dc30C1957379FAc9aba94D1C48a5405#code)
- [Martket place code from proxy](https://etherscan.io/address/0x52e75b420e70ea24bbd0ede2e71800a913cd06ee#code)

- `FoundationTreasuryNode` treasury contract address set on intilaize and expose get method
- `FNDNFTMarket` - initalizes treasuary, auction and resertvation
- 
