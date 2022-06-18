# NFT-contract-NEAR-PROTOCOL
## create a NFT contract implementing all NEAR blockchain  NFT standards
### PreRequisites
[Rust](https://docs.near.org/docs/develop/contracts/rust/intro#installing-the-rust-toolchain)  
[NEAR Wallet](https://docs.near.org/docs/develop/basics/create-account)  
[NEAR-CLI](https://docs.near.org/docs/tools/near-cli#setup)  

### Clone this Repo
Use `git clone https://github.com/LoboVH/NFT-contract-NEAR-PROTOCOL.git` to get the file in this in your localmachine.  
`cd NFT-contract-NEAR-PROTOCOL`



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
### Transfer function
Make a new account and transfer the token to that account 
Replace `"NEW ACCOUNT NAME"` with new account id.  
If you run the following command, it will transfer the token "token-1" to the account `"NEW ACCOUNT"` with the memo . Take note that you're also attaching exactly 1 yoctoNEAR by using the --depositYocto flag.
```near
    near call $NFT_CONTRACT_ID nft_transfer '{"receiver_id": "NEW ACCOUNT NAME", "token_id": "token-1", "memo": "ADD YOUR MEMO"}' --accountId $NFT_CONTRACT_ID --depositYocto 1
```
### Royalties
To mint a token with perpetual royalties, Run:
```near
    near call $NFT_CONTRACT_ID nft_mint '{"token_id": "royalty-token", "metadata": {"title": "ADD TITLE", "description": "ADD DESCRIPTION", "media": "ADD MEDIA LINK"}, "receiver_id": "'$NFT_CONTRACT_ID'", "perpetual_royalties": {"ROYALTY ACCOUNT NAME": ROYALTY_POINTS, "ROYALTY ACCOUNT NAME": ROYALTY_POINTS,
"ROYALTY ACCOUNT NAME": ROYALTY_POINTS}}' --accountId $NFT_CONTRACT_ID --amount 0.1
```
Update `token_id` and `metadata` field accordingly,
Replace `"ROYALTY ACCOUNT NAME"` with suitable Royalty account name also replace the `ROYALTY_POINTS` with the royalty percntage for the respective account.  
Minimum percentage you can give out is 0.01%, or 1 i.e. 10000 ROYALTY_POINTS is 100%.

To calculate NFT Payout for given balance:
```near
    near view $NFT_CONTRACT_ID nft_payout '{"token_id": "royalty-token", "balance": "BALANCE_IN_YOCTO-NEAR", "max_len_payout": 100}'
```
Note that `balance` is in yoctoNEAR.

### Approve account
#### Create a sub account
Run the following command to create a sub-account approval of your main account with an initial balance of 25 NEAR which will be transferred from the original to your new account.  
```near
    near create-account approval.$NFT_CONTRACT_ID --masterAccount $NFT_CONTRACT_ID --initialBalance 25
```
Export an environment variable for ease of development:
```near
    export APPROVAL_NFT_CONTRACT_ID=approval.$NFT_CONTRACT_ID
```
Using the build script, build the deploy the contract with new account.  
```near
    yarn build && near deploy --wasmFile out/main.wasm --accountId $APPROVAL_NFT_CONTRACT_ID
```
Initialize and Mint token:
```near
    near call $APPROVAL_NFT_CONTRACT_ID new_default_meta '{"owner_id": "'$APPROVAL_NFT_CONTRACT_ID'"}' --accountId $APPROVAL_NFT_CONTRACT_ID
```  
 mint a token with a token ID "approval-token" and the receiver will be your new account.  
 Update the metadata

```near
    near call $APPROVAL_NFT_CONTRACT_ID nft_mint '{"token_id": "approval-token", "metadata": {"title": "Title", "description": "Description", "media": "Media Link"}, "receiver_id": "'$APPROVAL_NFT_CONTRACT_ID'"}' --accountId $APPROVAL_NFT_CONTRACT_ID --amount 0.1
```
### Approving an account  
At this point, you should have two accounts. One stored under $NFT_CONTRACT_ID and the other under the $APPROVAL_NFT_CONTRACT_ID environment variable. You can use both of these accounts to test things out.  
Execute the following command to approve the account stored under $NFT_CONTRACT_ID to have access to transfer your NFT with an ID "approval-token".  
```near
    near call $APPROVAL_NFT_CONTRACT_ID nft_approve '{"token_id": "approval-token", "account_id": "'$NFT_CONTRACT_ID'"}' --accountId $APPROVAL_NFT_CONTRACT_ID --deposit 0.1
```
Run this command to check the new approved account ID being returned.  
```near
    near view $APPROVAL_NFT_CONTRACT_ID nft_tokens_for_owner '{"account_id": "'$APPROVAL_NFT_CONTRACT_ID'", "limit": 10}'
```
#### Transferring an NFT as an approved account  
You should be able to use the other account to transfer the NFT to itself by which the approved account IDs should be reset:  
pass the correct approval ID which is 0.  
```near
    near call $APPROVAL_NFT_CONTRACT_ID nft_transfer '{"receiver_id": "'$NFT_CONTRACT_ID'", "token_id": "approval-token", "approval_id": 0}' --accountId $NFT_CONTRACT_ID --depositYocto 1
```








