
## SecretPay Ledger Repository

### Overview
The SecretPay Ledger repository contains the necessary canister to run the ledger for the SecretPay application locally, enabling anonymous sending of gift cards with money through the ICP blockchain.

### Installation Instructions

#### Step 1: Install IC SDK
Ensure you have a recent version of the IC SDK installed. If not, follow the instructions in the IC SDK installation section.

#### Step 2: Create a New DFX Project
```bash
dfx new secretpay_ledger_canister
cd secretpay_ledger_canister
```

#### Step 3: Determine Ledger File Locations
Access the releases overview and copy the latest replica binary revision. For instance, the revision could be `d87954601e4b22972899e9957e800406a0a6b929`.

- **Ledger Wasm Module URL:** [Download Link](https://download.dfinity.systems/ic/<REVISION>/canisters/ledger-canister.wasm.gz)
- **Ledger .did File URL:** [Raw File Link](https://raw.githubusercontent.com/dfinity/ic/<REVISION>/rs/rosetta-api/icp_ledger/ledger.did)

[OPTIONAL] For the latest ICP ledger files:
```bash
curl -o download_latest_icp_ledger.sh "<DOWNLOAD_SCRIPT_LINK>"
chmod +x download_latest_icp_ledger.sh
./download_latest_icp_ledger.sh
```

#### Step 4: Configure dfx.json File
Modify the `dfx.json` file in your project directory with the provided content in the repository.

#### Step 5: Start a Local Replica
```bash
dfx start --background --clean
```

#### Step 6: Create a Minting Account Identity
```bash
dfx identity new secretpay_minter
dfx identity use secretpay_minter
export MINTER_ACCOUNT_ID=$(dfx ledger account-id)
```

#### Step 7: Record Default Identity's Ledger Account Identifier
```bash
dfx identity use default
export DEFAULT_ACCOUNT_ID=$(dfx ledger account-id)
```

#### Step 8: Deploy the Ledger Canister for SecretPay
```bash
dfx deploy --specified-id ryjl3-tyaaa-aaaaa-aaaba-cai secretpay_ledger_canister --argument "
  (variant {
    Init = record {
      minting_account = \"$MINTER_ACCOUNT_ID\";
      initial_values = vec {
        record {
          \"$DEFAULT_ACCOUNT_ID\";
          record {
            e8s = 10_000_000_000 : nat64;
          };
        };
      };
      send_whitelist = vec {};
      transfer_fee = opt record {
        e8s = 10_000 : nat64;
      };
      token_symbol = opt \"SEC\";
      token_name = opt \"Secret Coin\";
    }
  })
"
```

#### Step 9: Interact with the SecretPay Canister
Run CLI commands to interact with the canister:
```bash
dfx canister call secretpay_ledger_canister name
```
Here are some functions that you can run on the `secretpay_ledger_canister`:

### Available Functions

#### 1. **Get Token Name**
Retrieve the token's name.
```bash
dfx canister call secretpay_ledger_canister name
```

#### 2. **Get Token Symbol**
Fetch the token's symbol.
```bash
dfx canister call secretpay_ledger_canister symbol
```

#### 3. **Get Account Balance**
Check the balance of a specific account.
```bash
dfx canister call secretpay_ledger_canister getBalance <account_id>
```

#### 4. **Transfer Tokens**
Initiate a token transfer from one account to another.
```bash
dfx canister call secretpay_ledger_canister transfer <to_account_id> <amount>
```

#### 5. **Add to Whitelist**
Add an account to the send whitelist.
```bash
dfx canister call secretpay_ledger_canister addToWhitelist <account_id>
```

#### 6. **Set Transfer Fee**
Modify the transfer fee for transactions.
```bash
dfx canister call secretpay_ledger_canister setTransferFee <new_fee_amount>
```

Access the Candid UI at the deployed URL:
[Local Deployment Candid UI](http://127.0.0.1:4943/?canisterId=<CANISTER_ID>&id=ryjl3-tyaaa-aaaaa-aaaba-cai)

---
