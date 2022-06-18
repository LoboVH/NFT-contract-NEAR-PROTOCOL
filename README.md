# NFT-contract-NEAR-PROTOCOL
## create a NFT contract implementing all NEAR blockchain  NFT standards
### PreRequisites
[Rust](https://docs.near.org/docs/develop/contracts/rust/intro#installing-the-rust-toolchain)  
[NEAR Wallet](https://docs.near.org/docs/develop/basics/create-account)  
[NEAR-CLI](https://docs.near.org/docs/tools/near-cli#setup)  

### Clone this Repo
Use `git clone https://github.com/LoboVH/NFT-contract-NEAR-PROTOCOL.git` to get the file in this in your localmachine.

Run  
```yarn
    yarn build
```
to build the contract.  
Log in to your newly created account with near-cli by running the following command in your terminal  
```near
    near login
```
set an environment variable for your account ID. In the command below, replace `YOUR_ACCOUNT_NAME` with the account name you just logged in with including the `.testnet` portion:
```near
    export NFT_CONTRACT_ID="YOUR_ACCOUNT_NAME"
```
Test that the environment variable is set correctly by running:
```near
    echo $NFT_CONTRACT_ID
```
In the root of your NFT project run the following command to deploy your smart contract.
```near
    near deploy --wasmFile out/main.wasm --accountId $NFT_CONTRACT_ID
```
### Initializing the Contract
Initialize with default metadata.
```near
    near call $NFT_CONTRACT_ID new_default_meta '{"owner_id": "'$NFT_CONTRACT_ID'"}' --accountId $NFT_CONTRACT_ID
```
To view the contracts metadata.
```near
    near view $NFT_CONTRACT_ID nft_metadata
```
### Minting NFT
There are many fields that could potentially be stored on-chain: Check `TokenMetadata` struct at `nft-contract/src/metadata.rs`.
To mint an NFT with a title, description update respective fields in the following command,
```near
    near call $NFT_CONTRACT_ID nft_mint '{"token_id": "token-1", "metadata": {"title": "ADD TITLE", "description": "ADD DESCRIPTION", "media": "ADD MEDIA LINK"}, "receiver_id": "'$NFT_CONTRACT_ID'"}' --accountId $NFT_CONTRACT_ID --amount 0.1
```
To view information about the NFT.
```near
    near view $NFT_CONTRACT_ID nft_token '{"token_id": "token-1"}'
```
Navigate to the collectibles tab in the `NEAR wallet`, this should list all the NFTs that you own  
  
To get the total supply of NFTs owned by the your account, call the nft_supply_for_owner function and set the account_id parameter:
```near
    near view $NFT_CONTRACT_ID nft_supply_for_owner '{"account_id": "YOUR ACCOUNT NAME"}'
```
### Testing transfer function
Make a new account and transfer the token to that account 
Replace `"NEW ACCOUNT NAME"` with new account id.  
If you run the following command, it will transfer the token "token-1" to the account `"NEW ACCOUNT"` with the memo . Take note that you're also attaching exactly 1 yoctoNEAR by using the --depositYocto flag.
```near
    near call $NFT_CONTRACT_ID nft_transfer '{"receiver_id": "NEW ACCOUNT NAME", "token_id": "token-1", "memo": "ADD YOUR MEMO"}' --accountId $NFT_CONTRACT_ID --depositYocto 1
```




