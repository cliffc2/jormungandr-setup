
# How to load Jormungandr 0.8.x on Mac OSX 
> Dec 14, cliffc2 - Disclaimer: This is only for reference. During the roll out of the Shelley Incentivized testnet, anyone claiming to be a stake pool and requesting a transfer of ada is a scam. You will never need to send anyone your ada to delegate your stake. 

Here is the [IOHK Shelley testnet documentation](https://github.com/input-output-hk/shelley-testnet/blob/master/docs/stake_pool_operator_how_to.md)

Here is the [IOHK zendesk guide](https://github.com/cardano-foundation/incentivized-testnet-stakepool-registry/wiki#step-3-fund-your-account)

* To do list (overview)
  * [create github account](https://github.com/)
  * join the Telegram [CardanostakepoolWorkgroup](https://t.me/CardanostakepoolWorkgroup/176) for admin support
  * [download vscode](https://code.visualstudio.com/)
  * check sqlite version (mac osx comes preloaded with sqlite)
  * configure .bash_profile and .bashrc
  * make 2 folders (in terminal or finder)
      * jormungandr (inside home directory folder)
      * storage (inside home directory folder)
  * if you want to [download the binaries (zip) for osx](https://github.com/input-output-hk/jormungandr/releases) then  
      * unzip tar file in your download folder
      * drag and drop unzipped into your jormungandr folder 
  * if you want download from source then 
      * [install Rust](https://www.rust-lang.org/tools/install)
      * build jormungandr program from source (using the cargo command)
      * build jcli program from source (using the cargo command)
  * create a node config ".yaml" (in jormungandr folder)
    * edit (copy and paste) your (eg. config ".yaml") and 
    * change computer's ip address port number to 3100 
    * find the right [genesis block hash]() (add to .bashrc)
    * reload .bash_profile (.bashrc)
  * start jormungandr testnet 
  * check the testnet status (is the jormungandr node in 'sync'?) 
  * if you want rewards from the incentivized testnet (itn)
      * if yes - goto [cardano-wallet (itn)](https://github.com/cardano-foundation/incentivized-testnet-stakepool-registry/wiki/How-to-migrate-a-Byron-wallet-using-the-command-line)
        * create a 15 word password
        * create a secret key from 15 word 
      * if no - you can create an account from a script
        * download script
        * change permission
        * run script
  * if you do not want rewards but want to build from scratch (nightly)
      * create a secret key
      * create a public key 
      * create an account address
  * then get tokens from faucet (or telegram group)
  * check your account balance
  * if balance is (+) send test ada tokens to another account address
  * delegate to stakepool
  * create stake pool keys (4)
  * create stake pool certificate from stake pool keys
  * get node id
  * create your owner keys
  * register your stakepool for incentives

  
> IOHK setup references https://iohk.zendesk.com/hc/en-us
  

Download the Jormungandr 0.8.x OSX zip file
--- 
> View latest releases https://github.com/input-output-hk/jormungandr/releases/ 

* look for jormungandr-v0.8.2-x86_64-apple-darwin.tar.gz 
* download it
* unzip the tar
* open the "home" folder in finder (command+shift+H)
* create a new folder called jormungandr in the home directory
* drag and drop the 2 files (jcli and jormungandr programs) into jormungandr folder

>OR you can type this in the Terminal to make 2 folders

```
mkdir -p ~/jormungandr     
mkdir -p ~/storage 
```
>  This 'storage' location is declared in the .bashrc file
---




Create and edit a new file called itn_rewards_v1-config.yaml
---

```
cd jormungandr 
nano itn_rewards_v1-config.yaml
```  

>Copy the itn_rewards_v1-config.yaml below and open nano editor (or vscode) config needs to be in jormungandr folder - shortcuts for 
copy (command+c) and paste (command+v) then save (control+o) and close nano (control+x) 


Copy this node config for itn_rewards_v1-config.yaml for 0.8.2 (itn)
---
```
{
  "log": [
    {
      "format": "plain",
      "level": "info",
      "output": "stderr"
    }
  ],
  "p2p": {
    "topics_of_interest": {
      "blocks": "high",
      "messages": "high"
    },
    "trusted_peers": [
      {
        "address": "/ip4/52.9.132.248/tcp/3000",
        "id": "671a9e7a5c739532668511bea823f0f5c5557c99b813456c"
      },
      {
        "address": "/ip4/52.8.15.52/tcp/3000",
        "id": "18bf81a75e5b15a49b843a66f61602e14d4261fb5595b5f5"
      },
      {
        "address": "/ip4/13.114.196.228/tcp/3000",
        "id": "7e1020c2e2107a849a8353876d047085f475c9bc646e42e9"
      },
      {
        "address": "/ip4/13.112.181.42/tcp/3000",
        "id": "52762c49a84699d43c96fdfe6de18079fb2512077d6aa5bc"
      },
      {
        "address": "/ip4/3.125.75.156/tcp/3000",
        "id": "22fb117f9f72f38b21bca5c0f069766c0d4327925d967791"
      },
      {
        "address": "/ip4/52.28.91.178/tcp/3000",
        "id": "23b3ca09c644fe8098f64c24d75d9f79c8e058642e63a28c"
      },
      {
        "address": "/ip4/3.124.116.145/tcp/3000",
        "id": "99cb10f53185fbef110472d45a36082905ee12df8a049b74"
      }
    ]
  },
  "rest": {
    "listen": "127.0.0.1:3100"
  }
}
```
>Troubleshooting - name your configs according to version so you can see them clearly - the storage location is separated from previous rc versions it will be declared in .bashrc

Configure .bash_profile file 
---
>Open the .bash_profile file in nano. The period in .bash_profile denotes the file is hidden in the finder (hold command+shift+[period key] to show hidden files in finder). Note - these are forked from Chris Graffagnino (linux) https://github.com/Chris-Graffagnino/Jormungandr-for-Newbs/tree/master/config



```
nano ~/.bash_profile
```
>Copy and paste the following into the .bash_profile file 
```
#this is a fork so not all of the simplified commands work yet
export ARCHFLAGS="-arch x86_64"
test -f ~/.bashrc && source ~/.bashrc

#------- testing starts here ---- cliffc2 ---- 

alias c-acct='command cat receiver_account.txt'
alias c-pk='command cat receiver_public.key'
alias c-acct='command cat receiver_account.txt'
alias run-itn='command jormungandr --config itn_rewards_v1-config.yaml --genesis-block-hash ${GENESIS_BLOCK_HASH_ITN}'

# nightly-config-082
function start-082n() {
    GREEN=$(printf "\033[0;32m")
    nohup jormungandr --config nightly-config-082.yaml --genesis-block-hash $GENESIS_BLOCK_HASH_082N >> logs/node.out 2>&1 &
    echo ${GREEN}$(ps | grep jormungandr)
}

function check-gb() {
    echo "${GENESIS_BLOCK_HASH}"
}

function gen-owner() {
    echo "$(jcli key generate --type ed25519 | tee owner.prv | jcli key to-public > owner.pub | cat owner.{prv,pub})"
}

function gen-paddr() {
    echo "$(jcli address account --prefix addr --testing $(cat receiver_public.key) | tee receiver_account.txt)"
}
#-------- end test functions ------

function stop() {
    echo "$(jcli rest v0 shutdown get -h http://127.0.0.1:${REST_PORT}/api)"
}

function stats() {
    echo "$(jcli rest v0 node stats get -h http://127.0.0.1:${REST_PORT}/api)"
}

function bal() {
    echo "$(jcli rest v0 account get $(cat receiver_account.txt) -h  http://127.0.0.1:${REST_PORT}/api)"
}

function faucet() {
    echo "$(curl -X POST https://faucet.beta.jormungandr-testnet.iohkdev.io/send-money/$(cat ~/jormungandr/receiver_account.txt))"
}

function get_ip() {
    echo "${PUBLIC_IP_ADDR}"
}

function get_pid() {
    ps auxf | grep jor
}

function memory() {
    top -o %MEM
}

function nodes() {
    nodes="$(netstat -tupan | grep jor | grep EST | cut -c 1-80)"
    total="$(netstat -tupan | grep jor | grep EST | cut -c 1-80 | wc -l)"
    printf "%s\n" "${nodes}" "----------" "Total:" "${total}"
}

function num_open_files() {
    echo "Calculating number of open files..."
    echo "$(lsof -u $(whoami) | wc -l)"
}

function is_pool_visible() {
    echo ${GREEN}$(jcli rest v0 stake-pools get --host "http://127.0.0.1:${REST_PORT}/api" | grep $(cat stake_pool.id))
}

function create_stake_pool() {
    echo "$(createStakePool.sh ${REST_PORT} $(cat receiver_secret.key))"
}

function delegate() {
    echo "$(delegate-account.sh $(cat stake_pool.id) ${REST_PORT} $(cat receiver_secret.key))"
}


function leader_logs() {
    echo "Has this node been scheduled to be leader?"
    echo "$(jcli rest v0 leaders logs get -h http://127.0.0.1:${REST_PORT}/api)"
}

function problems() {
    grep -E -i 'cannot|stuck|exit|unavailable' logs/node.out
}
```
---

Configure .bashrc
---
```
nano ~/.bashrc
``` 
>Here is and example of the .bashrc - copy and paste the text below. This will simplify the different GB hashes you may want to call. 
```

# this is a list of genesis block hashes you may want to try.
export USERNAME='<USERNAME>'
export PUBLIC_IP_ADDR='<YOU CAN FIND THIS FROM IFCONFIG IN COMMAND LINE>'
export REST_PORT='<THE MOST COMMON PORT BEING USED IS 3100>'
export JORMUNGANDR_STORAGE_DIR='/home/TYPE_USERNAME/storage'
export RUST_BACKTRACE=1
export GENESIS_BLOCK_HASH='8e4d2a343f3dcf9330ad9035b3e8d168e6728904262f2c434a4f8f934ec7b676'
export GENESIS_BLOCK_HASH_ITN='8e4d2a343f3dcf9330ad9035b3e8d168e6728904262f2c434a4f8f934ec7b676'
export GENESIS_BLOCK_HASH_082N='9409af111b04896c756c1cee3b7f9bae8b9ed1843c9e0a5f07d92ab9b62f6f78'
export GENESIS_BLOCK_HASH_080L='e03547a7effaf05021b40dd762d5c4cf944b991144f1ad507ef792ae54603197'
```

Type each of the following commands in terminal
---
> Forked from Chris Graffagnino's Gist https://github.com/Chris-Graffagnino/Jormungandr-for-Newbs
```
echo "export USERNAME='<Replace this with your USERNAME>'" >> ~/.bashrc
```
> Open a new terminal prompt (command+N) replace `<YOUR USERNAME>` with your mac's username. you can find the username with the command ```ls /users ```
```
echo "export PUBLIC_IP_ADDR='<YOUR PUBLIC IP ADDRESS>'" >> ~/.bashrc
```
> replace `<YOUR PUBLIC IP ADDRESS>` with your inet ip address. use the command ```ifconfig``` or ``` k``` look for the number set after "inet" eg. 12.18.43.10
```
echo "export REST_PORT='3100'" >> ~/.bashrc
```
> This is a common port number in the node configs yaml - 3100 - being used by most of the admins 
```
echo "export JORMUNGANDR_STORAGE_DIR='storage'" >> ~/.bashrc
```
> copy this and enter. this matches the storage location in the common node configs 
```
echo "export GENESIS_BLOCK_HASH='<EXAMPLE-GBLOCK-HASH>'" >> ~/.bashrc
```
>replace `<EXAMPLE-GBLOCK-HASH>` with the itn or the nightly hash (see below)

> Chris Graffagnino notes - What did we just do?
"echo" essentially means "print to screen"
"export" declares a variable in a special way, so that any shells that spawn from it inherit the variable.">>" means "take the output of the previous command and append it to the end of a file (.bashrc, in this case)

Load the new .bash_profile and .bashrc 
---
```
source ~/.bash_profile
```

>The source command loads new variables so you can use them in the terminal. FYI There's a command in .bash_profile that points to .bashrc 



---
Start the Jormungandr Node 0.8.2 (itn or nightly)
---
>Check for the latest genesis block hashes and recent configs ".yaml". The paths if working correctly will use the .bash_profile functions to shorten the commands and environment variables. Also check your ports to make sure they are calling the right number (like phone extentions). Check your ip address and port (eg. 127.0.0.1:3100)

This is the command to start Jormungandr with Daedalus / Yoroi rewards
```
jormungandr --config itn_rewards_v1-config.yaml --genesis-block-hash ${GENESIS_BLOCK_HASH}
```
>Genesis block hash for 0.8.2 itn_rewards_v1
`8e4d2a343f3dcf9330ad9035b3e8d168e6728904262f2c434a4f8f934ec7b676`

This is the command to start Jormungandr without Daedalus / Yoroi rewards 
```
jormungandr --config nightly-config-082.yaml --genesis-block-hash ${GENESIS_BLOCK_HASH}
```
> Genesis block hash for 0.8.2 nightly `9409af111b04896c756c1cee3b7f9bae8b9ed1843c9e0a5f07d92ab9b62f6f78`

You can check the node to see if it stared correctly
```
tail logs/node.out
```
>Troubleshooting note: check for the latest genesis block hash and config.yaml path. Also check your ports to make sure they are calling the right number. Check your ip address and port (127.0.0.1:3100) 



---

Check the node - is it in 'sync'?
---
> Open a new Terminal > Shell > New window (command+N) if the .bash_profile is written correctly you can use stats 

```
 stats 
 ``` 
 >it would be a shortcut for the jcli rest v0 command below
 ```
 jcli rest v0 node stats get -h http://127.0.0.1:3100/api
 ``` 

>or check the node stats with curl (same as jcli rest v0 node stats command)

```
curl http://127.0.0.1:3100/api/v0/node/stats
```

---

Delegate your tokens to a stakepool
---
>If you are not operating a stakepool then delegate
https://github.com/input-output-hk/jormungandr-qa/tree/master/scripts 

``` 
delegate-account.sh <STAKE_POOL_ID> <REST-LISTEN-PORT> <ACCOUNT-SK> 
```

> View the stakepools in your firefox browser (json format) when running

 ```
 http://127.0.0.1:3100/api/v0/stake_pools
 ```


  
---



Check your fee settings
---
```
jcli rest v0 settings get -h http://127.0.0.1:3100/api
``` 



---
Create your account address (with incentives)
---

>If you want to track your rewards in Daedalus or Yoroi you need to get the cardano-wallet program to generate a 15 word seed - on the 0.8.2 itn blockchain. 
```
mkdir ~/.local/bin

curl -L https://github.com/input-output-hk/cardano-wallet/releases/download/v2019-12-09/cardano-wallet-jormungandr-macos64-v2019-12-09.tar.gz | tar xz -C $HOME/.local/bin

# Show help
cardano-wallet -h

# Generate 15 word mnemonic phrase
cardano-wallet mnemonic generate

# Restore the private key from 15 word mnemonic phrase
cardano-wallet mnemonic reward-credentials

# Create a secret key file and copy and paste to nano editor
nano receiver_secret.key
```

>You can find the script here 
https://github.com/input-output-hk/cardano-wallet/wiki/Wallet-command-line-interface




> For more information refer to this [How to register your stakepool on chain](https://github.com/cardano-foundation/incentivized-testnet-stakepool-registry/wiki/How-to-Register-Your-Stake-Pool-on-Chain)

---

Create your account address (script without incentives)
---


>Download this `createAddress.sh` script (not recognized by Daedalus or Yoroi)

```
curl -sLOJ https://raw.githubusercontent.com/input-output-hk/jormungandr-qa/master/scripts/createAddress.sh
```
>Change permissions (make executable +x)

```
chmod +x createAddress.sh
```

>Run the create account address script

```
createAddress.sh account
```

To manually create an account address with the "addr" prefix. 
---
>
```
jcli address account --prefix addr --testing $(cat receiver_public.key) | tee receiver_account.txt
```
>You can rename the txt file to receiver_account-addr082.txt later to back it up. 

---

Get tokens (testnet ADA from telegram group if faucet is out)
---

 https://testnet.iohkdev.io/en/cardano/shelley/tools/faucet/

 ---
 
Check your account address to see your token balance 
---
>Or you can use the shell function -  ```bal```
```
jcli rest v0 account get $(cat receiver_account.txt) -h  http://127.0.0.1:3100/api
``` 

---

Delegate tokens to a stakepool (script)
---


> https://github.com/input-output-hk/jormungandr-qa/tree/master/scripts 

``` 
delegate-account.sh <STAKE_POOL_ID> <REST-LISTEN-PORT> <ACCOUNT-SK> 
``` 


Send tokens to an account address (script)
---

> Create send-lovelaces.sh script 
```
nano send-lovelaces.sh
```
>copy and paste the [send-lovelaces code from here](https://github.com/Chris-Graffagnino/Jormungandr-for-Newbs/blob/master/scripts/send-lovelaces.sh) 

> change permissions to make the file executable 

```
chmod +x ./send-lovelaces.sh
```

> Send tokens format go
```
send-lovelaces.sh <DESTINATION ADDRESS> <AMOUNT LOVELACES TO SEND> ${REST_PORT} $(cat receiver_secret.key)
```
> here are the steps below https://github.com/cliffc2/jormungandr-setup#send-tokens-to-another-account

---



#  How to load Jormungandr from source code (Intermediate system administrator skill level)

* To do list (load from source code overview)
  * install the [Rust programming language](https://github.com/rust-lang.)
  * * make 2 folders (for testnet program and blockchain database)
  * download jormungandr program (using the git and cargo command)
  * download jcli (using the git and cargo command)
  * load sqlite (if not loaded - for blockchain database)
  * create config.yaml (computer's node info - ip address and port)
  * edit port 
  * start jormungandr node -0.8.2. itnv1 
  * check node is in 'sync'
  * create secret keys 
  * create public keys from secret keys 
  * create account address (prefix addr) from public keys 
  * get tokens from faucet or telegram group
  * send tokens to another account
  * create stake pool keys (4)
  * create stake pool certificate from stake pool keys
  * get node id
  * create your owner keys
  * register your stakepool for incentives

---

Download Rust 
---


```
curl https://sh.rustup.rs -sSf | sh
```

> For reference https://www.rust-lang.org/tools/install


Create Rust path 
---
> source loads rustup path to bash_profile

 ```
 source $HOME/.cargo/env
 ``` 
 

Install Rust 
---
 ```
 rustup install stable
 ``` 
 
>Load Default (toolchain set to 'stable-x86_64-apple-darwin') 

```
rustup default stable
``` 

>Check the Rust version (rustc 1.38.0) 

 ```
 rustc --version
 ``` 

>Update if this is an old version

```
rustup update
```

>Show folder paths if Rust fails 
 
```
echo $PATH
```

> https://explainshell.com/explain?cmd=echo+%24PATH

---
Download Jormungandr from IOHK github
---
>Check out the latest tags https://github.com/input-output-hk/jormungandr/releases/latest

``` 
git clone --recurse-submodules https://github.com/input-output-hk/jormungandr 
```

>Create and enter the Jormungandr folder

 ```
 cd jormungandr
 ``` 

Build jormungandr from source
---
 ```
 cargo install --path jormungandr
 ```

Build jcli from source
--- 
```
cargo install --path jcli
```

Check the jcli version
--- 
 ```
 jcli -V
 ``` 

> If build fails - load ```xcode-select --install```  
Trouble shooting note: **[xcrun: error: invalid active developer path](https://stackoverflow.com/questions/52522565/git-is-not-working-after-macos-update-xcrun-error-invalid-active-developer-pa)** 
error: missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun 


# Choose a blockchain testnet and genesis block hash to run

```
GENESIS_BLOCK_HASH
0.7.0           27668e95121566df0bb2e2c11c5fd95dfe59efd570f8f592235ecff167ca3f29
0.8.0 rc10      65a9b15f82619fffd5a7571fdbf973a18480e9acf1d2fddeb606ebb53ecca839  
0.8.0           65a9b15f82619fffd5a7571fdbf973a18480e9acf1d2fddeb606ebb53ecca839  
0.8.0 legacy    e03547a7effaf05021b40dd762d5c4cf944b991144f1ad507ef792ae54603197
0.8.2 nightly   9409af111b04896c756c1cee3b7f9bae8b9ed1843c9e0a5f07d92ab9b62f6f78
0.8.2 itnv1     8e4d2a343f3dcf9330ad9035b3e8d168e6728904262f2c434a4f8f934ec7b676

```

Choose the right config yaml for the corresponding genesis block 
---
>Search the hydra for the recent configs \
https://hydra.iohk.io/search?query=configs



Create and edit a new file called itn_rewards_v1-config.yaml
---
https://hydra.iohk.io/job/Cardano/iohk-nix/jormungandrConfigs.itn_rewards_v1
```
cd jormungandr 
nano itn_rewards_v1-config.yaml
```  

>Download the itn_rewards_v1-config.yaml https://hydra.iohk.io/search?query=config and open nano editor (or vscode) config needs to be in jormungandr folder 

>shortcuts for 
copy (command+c) and paste (command+v) then save (control+o) and close nano (control+x) 


Node config for itn_rewards_v1-config.yaml for 0.8.2 (itn)
---
```
{
  "log": [
    {
      "format": "plain",
      "level": "info",
      "output": "stderr"
    }
  ],
  "p2p": {
    "topics_of_interest": {
      "blocks": "high",
      "messages": "high"
    },
    "trusted_peers": [
      {
        "address": "/ip4/52.9.132.248/tcp/3000",
        "id": "671a9e7a5c739532668511bea823f0f5c5557c99b813456c"
      },
      {
        "address": "/ip4/52.8.15.52/tcp/3000",
        "id": "18bf81a75e5b15a49b843a66f61602e14d4261fb5595b5f5"
      },
      {
        "address": "/ip4/13.114.196.228/tcp/3000",
        "id": "7e1020c2e2107a849a8353876d047085f475c9bc646e42e9"
      },
      {
        "address": "/ip4/13.112.181.42/tcp/3000",
        "id": "52762c49a84699d43c96fdfe6de18079fb2512077d6aa5bc"
      },
      {
        "address": "/ip4/3.125.75.156/tcp/3000",
        "id": "22fb117f9f72f38b21bca5c0f069766c0d4327925d967791"
      },
      {
        "address": "/ip4/52.28.91.178/tcp/3000",
        "id": "23b3ca09c644fe8098f64c24d75d9f79c8e058642e63a28c"
      },
      {
        "address": "/ip4/3.124.116.145/tcp/3000",
        "id": "99cb10f53185fbef110472d45a36082905ee12df8a049b74"
      }
    ]
  },
  "rest": {
    "listen": "127.0.0.1:3100"
  }
}
```

Check the port and node configuration
---

```
echo ${GENESIS_BLOCK_HASH}
```
>Note if you added the .bashrc exports and shell functions correctly - these will show the default and optional hashes change them in ```nano ~/.bash_profile``` and ```source ~/.bash_profile``` after editing to refresh shell
```
echo ${GENESIS_BLOCK_HASH_ITN}
echo ${GENESIS_BLOCK_HASH_082N}
echo ${GENESIS_BLOCK_HASH_080L}
```

 


Start 0.8.2 itn_rewards_v1 - testnet
---
```
jormungandr --config itn_rewards_v1-config.yaml --genesis-block-hash ${GENESIS_BLOCK_HASH_ITN}
```
Start 0.8.2 nightly - testnet
---
```
jormungandr --config nightly-config-082.yaml --genesis-block-hash ${GENESIS_BLOCK_HASH_082N}
```
>Check to see if node is receiving blocks ```stats``` or full jcli cmd
```
jcli rest v0 node stats get -h http://127.0.0.1:3100/api
```
> You can also view this in firefox http://127.0.0.1:3100/api/v0/node/stats
```
curl http://127.0.0.1:3100/api/v0/node/stats
```

> Now we can make keys with this script here  https://github.com/input-output-hk/jormungandr-qa/tree/master/scripts

```
createAddress.sh account
```

# Create your secret keys, public and address account for Daedulus (from scratch)

>To be recognized by Daedalus and Yoroi you need to get a secret key from the cardano-wallet https://github.com/input-output-hk/cardano-wallet/wiki/Wallet-command-line-interface
```
mkdir ~/.local/bin

# Download from IOHK github
curl -L https://github.com/input-output-hk/cardano-wallet/releases/download/v2019-12-09/cardano-wallet-jormungandr-macos64-v2019-12-09.tar.gz | tar xz -C $HOME/.local/bin

# Show help
cardano-wallet -h

# Generate your 15 word mnemonic phrase
cardano-wallet mnemonic generate

# Make (restore) the private key from 15 word mnemonic phrase
cardano-wallet mnemonic reward-credentials

#This is your new secret key from the cardano-wallet
```

Copy and paste your cardano-wallet secret key to a file called receiver_secret.key (KEEP THIS SAFE)
---
```
nano receiver_secret.key
```
Make a public key from the secret key and save it to a file called receiver_public.key
---

```
cat receiver_secret.key | jcli key to-public > receiver_public.key
``` 
Make an account address (with addr prefix) from the public key and save it to a file called receiver_account.txt
--- 
```
jcli address account --prefix addr --testing $(cat receiver_public.key) | tee receiver_account.txt

#make a backup and save to receiver_account_itn-addr.txt
jcli address account --prefix addr --testing $(cat receiver_public.key) | tee receiver_account_082-addr.txt
```

Make an account address (without addr prefix) from the public key and save to receiver_account-acct.txt
---

```
jcli address account --testing $(cat receiver_public.key) | tee receiver_account-acct.txt

#Make a backup 
jcli address account --testing $(cat receiver_public.key) | tee receiver_account_acct-bu.txt
``` 


> View the content of the file

```
cat receiver_account.txt
cat receiver_public.key
cat receiver_secret.key
```


>List files and folders in jormungandr

```
ls 
``` 

> You should see receiver_account.txt, receiver_secret.key, and a receiver_public.key \
IT IS EXTREMELY IMPORTANT THAT YOU SAVE YOUR KEYS FOR FUTURE USE
---

Get testnet ADA tokens (test-ADA)
---
 https://testnet.iohkdev.io/en/cardano/shelley/tools/faucet/
```
curl -X POST https://faucet.faucet.jormungandr-testnet.iohkdev.io/send-money/$(cat receiver_account.txt)
```
>if the faucet does not work, go to the telegram group and ask 
---
Check your account address to see your token balance 
---
```
jcli rest v0 account get $(cat receiver_account.txt) -h  http://127.0.0.1:3100/api
``` 
> or type ``` bal``` if the bash_profile is configured correctly
---
Send tokens to another account
---
>Now we can send lovelaces (test-ADA) using a script. 

```
send-lovelaces.sh <ADDRESS> <AMOUNT> <REST-LISTEN-PORT> <SOURCE-SK>

send-lovelaces.sh addr1sak47... 2000000 ${REST_PORT} $(cat receiver_secret.key)
```
>open a new file in nano called send-lovelaces.sh

```
nano send-lovelaces.sh 
``` 
>now copy and paste into nano

```
#!/bin/sh forked from chris graffagnino

# Disclaimer:
#
#  The following use of shell script is for demonstration and understanding
#  only, it should *NOT* be used at scale or for any sort of serious
#  deployment, and is solely used for learning how the node and blockchain
#  works, and how to interact with everything.
#
#  It also asumes that `jcli` is in the same folder with the script.
#  The script works only for Account addresses type.



### CONFIGURATION
CLI="jcli"
COLORS=1
ADDRTYPE="--testing"
TIMEOUT_NO_OF_BLOCKS=200

getTip() {
  echo $($CLI rest v0 tip get -h "${REST_URL}")
}

waitNewBlockCreated() {
  COUNTER=${TIMEOUT_NO_OF_BLOCKS}
  echo "  ##Waiting for new block to be created (timeout = $COUNTER blocks = $((${COUNTER} * ${SLOT_DURATION}))s)"
  initialTip=$(getTip)
  actualTip=$(getTip)

  while [ "${actualTip}" = "${initialTip}" ]; do
    sleep ${SLOT_DURATION}
    actualTip=$(getTip)
    COUNTER=$((COUNTER - 1))
    if [ ${COUNTER} -lt 2 ]; then
      echo "  ##ERROR: Waited $(($COUNTER * $SLOT_DURATION))s secs ($COUNTER*$SLOT_DURATION) and no new block created"
      exit 1
    fi
  done
  echo "New block was created - $(getTip)"
}

### COLORS
if [ ${COLORS} -eq 1 ]; then
  GREEN=$(printf "\033[0;32m")
  RED=$(printf "\033[0;31m")
  BLUE=$(printf "\033[0;33m")
  WHITE=$(printf "\033[0m")
else
  GREEN=""
  RED=""
  BLUE=""
  WHITE=""
fi

if [ $# -ne 4 ]; then
  echo "usage: $0 <ADDRESS> <AMOUNT> <REST-LISTEN-PORT> <SOURCE-SK>"
  echo "    <ADDRESS>     Address where to send the funds"
  echo "    <AMOUNT>      Amount to be sent (in lovelace) - tx fees will be paid by the source address"
  echo "    <REST-LISTEN-PORT>   The REST Listen Port set in node-config.yaml file (EX: 3101)"
  echo "    <SOURCE-SK>   The Secret key of the Source address"
  exit 1
fi

DESTINATION_ADDRESS="$1"
DESTINATION_AMOUNT="$2"
REST_PORT="$3"
SOURCE_SK="$4"

REST_URL="http://127.0.0.1:${REST_PORT}/api"
BLOCK0_HASH=$($CLI rest v0 settings get -h "${REST_URL}" | grep 'block0Hash:' | sed -e 's/^[[:space:]]*//' | sed -e 's/block0Hash: //')
FEE_CONSTANT=$($CLI rest v0 settings get -h "${REST_URL}" | grep 'constant:' | sed -e 's/^[[:space:]]*//' | sed -e 's/constant: //')
FEE_COEFFICIENT=$($CLI rest v0 settings get -h "${REST_URL}" | grep 'coefficient:' | sed -e 's/^[[:space:]]*//' | sed -e 's/coefficient: //')
MAX_TXS_PER_BLOCK=$($CLI rest v0 settings get -h "${REST_URL}" | grep 'maxTxsPerBlock:' | sed -e 's/^[[:space:]]*//' | sed -e 's/maxTxsPerBlock: //')
SLOT_DURATION=$($CLI rest v0 settings get -h "${REST_URL}" | grep 'slotDuration:' | sed -e 's/^[[:space:]]*//' | sed -e 's/slotDuration: //')
SLOTS_PER_EPOCH=$($CLI rest v0 settings get -h "${REST_URL}" | grep 'slotsPerEpoch:' | sed -e 's/^[[:space:]]*//' | sed -e 's/slotsPerEpoch: //')

echo "================Send Money================="
echo "DESTINATION_ADDRESS: ${DESTINATION_ADDRESS}"
echo "DESTINATION_AMOUNT: ${DESTINATION_AMOUNT}"
echo "REST_PORT: ${REST_PORT}"
echo "SOURCE_SK: ${SOURCE_SK}"
echo "BLOCK0_HASH: ${BLOCK0_HASH}"
echo "FEE_CONSTANT: ${FEE_CONSTANT}"
echo "FEE_COEFFICIENT: ${FEE_COEFFICIENT}"
echo "=================================================="

STAGING_FILE="staging.$$.transaction"

#CLI transaction
if [ -f "${STAGING_FILE}" ]; then
  echo "error: staging already exist. restart"
  exit 2
fi

set -e

SOURCE_PK=$(echo ${SOURCE_SK} | $CLI key to-public)
SOURCE_ADDR=$($CLI address account ${ADDRTYPE} ${SOURCE_PK})

echo "## Sending ${RED}${DESTINATION_AMOUNT}${WHITE} to ${BLUE}${DESTINATION_ADDRESS}${WHITE}"
$CLI address info "${DESTINATION_ADDRESS}"

# TODO we should do this in one call to increase the atomicity, but otherwise
SOURCE_COUNTER=$($CLI rest v0 account get "${SOURCE_ADDR}" -h "${REST_URL}" | grep '^counter:' | sed -e 's/counter: //')

# the source account is going to pay for the fee ... so calculate how much
# FEE_COEFFICIENT should be multiplied witht the no of (INPUTS + OUTPUTS) - we use only 1 Source and 1 Destination
ACCOUNT_AMOUNT=$((${DESTINATION_AMOUNT} + ${FEE_CONSTANT} + $((2 * ${FEE_COEFFICIENT}))))

# Create the transaction
# FROM: ACCOUNT for AMOUNT+FEES
# TO: DESTINATION ADDRESS for AMOUNT
echo " ##1. Create the offline transaction file"
$CLI transaction new --staging ${STAGING_FILE}

echo " ##2. Add input details"
$CLI transaction add-account "${SOURCE_ADDR}" "${ACCOUNT_AMOUNT}" --staging "${STAGING_FILE}"

echo " ##3. Add output details"
$CLI transaction add-output "${DESTINATION_ADDRESS}" "${DESTINATION_AMOUNT}" --staging "${STAGING_FILE}"

echo " ##4. Finalize the transactions"
$CLI transaction finalize --staging ${STAGING_FILE}

TRANSACTION_ID=$($CLI transaction data-for-witness --staging ${STAGING_FILE})

echo " ##5. Create the witness"
# Create the witness for the 1 input (add-account) and add it
WITNESS_SECRET_FILE="witness.secret.$$"
WITNESS_OUTPUT_FILE="witness.out.$$"

printf "${SOURCE_SK}" >${WITNESS_SECRET_FILE}

$CLI transaction make-witness ${TRANSACTION_ID} \
  --genesis-block-hash ${BLOCK0_HASH} \
  --type "account" --account-spending-counter "${SOURCE_COUNTER}" \
  ${WITNESS_OUTPUT_FILE} ${WITNESS_SECRET_FILE}

echo " ##6. Add the witness to the transaction"
$CLI transaction add-witness ${WITNESS_OUTPUT_FILE} --staging "${STAGING_FILE}"

echo " ##7. Show the transaction info"
$CLI transaction info --fee-constant ${FEE_CONSTANT} --fee-coefficient ${FEE_COEFFICIENT} --staging "${STAGING_FILE}"

echo " ##7. Finalize the transaction and send it"
$CLI transaction seal --staging "${STAGING_FILE}"
$CLI transaction to-message --staging "${STAGING_FILE}" | $CLI rest v0 message post -h "${REST_URL}"

echo " ##8. Remove the temporary files"
rm ${STAGING_FILE} ${WITNESS_SECRET_FILE} ${WITNESS_OUTPUT_FILE}

waitNewBlockCreated

echo " ##9. Check the account's balance"
$CLI rest v0 account get ${DESTINATION_ADDRESS} -h ${REST_URL}

exit 0
```
> reference https://github.com/input-output-hk/jormungandr-qa/tree/master/scripts 

---
Check the message log to see the transaction
---
```
jcli rest v0 message logs --host "http://127.0.0.1:3100/api
```
>Check the balance of the account - or type ``` bal ```
```
jcli rest v0 account get $(cat receiver_account.txt) -h  http://127.0.0.1:3100/api
``` 



 ---


Create a Stake pool (key and cert) example
---
>These commands will generate your stake pool id (node-id) and the node_secret.yaml file. You need to make 4 keys; kes.prv, kes.pub, vrf.prv, vrf.pub


Generate your KES/VRF key pair
---
> Here is the Cardano Foundation example format 
```
  jcli key generate --type=SumEd25519_12 > kes.prv
  jcli key to-public < kes.prv > kes.pub
  jcli key generate --type=Curve25519_2HashDH > vrf.prv
  jcli key to-public < vrf.prv > vrf.pub
```
|   | OSX Terminal |
| ------------- | ------------- |
| Make a secret vrf key  | ```        jcli key generate --type=Curve25519_2HashDH > vrf.prv         ```  |
| Make a public vrf key from secret vrf key  | ```cat vrf.prv jcli key to-public > vrf.pub```  |
| Make a secret stakepool key from scratch  | ```jcli key generate --type=SumEd25519_12 > kes.prv``` |
| Make a public stakepool key from secret | ```cat kes.prv jcli key to-public > kes.pub``` |

Get your stake pool cert - generate your pool registration certificate
---

>This is the format with indentions from [Cardano Foundation reference](https://github.com/cardano-foundation/incentivized-testnet-stakepool-registry/wiki/How-to-Register-Your-Stake-Pool-on-Chain#step-4-generate-your-pools-credentials) 
```    
jcli certificate new stake-pool-registration \
    --kes-key $(cat kes.pub) \
    --vrf-key $(cat vrf.pub) \
    --owner $(cat owner.pub) \
    --management-threshold 1 \
    --tax-limit $TAX_LIMIT \
    --tax-ratio $TAX_RATIO \
    --tax-fixed $TAX_FIXED \
    --start-validity 0 \
    > stake-pool-registration.cert
```
>

```    
#this is a depreciated example - for reference only

jcli certificate new stake-pool-registration \
    --kes-key $(cat stake_pool_kes.pub) \
    --vrf-key $(cat stake_pool_vrf.pub) \
    --start-validity 0 \
    --management-threshold 1 \
    --tax-fixed 1000000 \
    --tax-limit 1000000000 \
    --tax-ratio "1/10" \
    --owner $(cat owner_key.pub) > stake_pool.cert
```
>Example new stakepool registration in one line
```
jcli certificate new stake-pool-registration --kes-key kes25519-12-pk1q06kvadqp040wzc5acnnv06rjqns8aphnavyf9xfgpf6awjnnptqef9jkk --vrf-key vrf_pk1sesgrk2k6e6rxypkcj855fnnw8cs9k5zg62yhqklrzshmlj02qysdhxeqy --owner ed25519_pk14pe9kt0kcxqlj7h8g3ye2ljt5mjlph0y08l2jg8u6hgwsgag830qd708gl --start-validity 0 --management-threshold 1 > stake_pool.cert

```
Get the list of stake pools
---
```
jcli rest v0 stake-pools get --host "http://127.0.0.1:3100/api"
```
Incentivized Testnet Stake Pool Registry
---
> Here is the Cardano Foundation github https://github.com/cardano-foundation/incentivized-testnet-stakepool-registry/wiki/How-to-Register-Your-Stake-Pool-on-Chain

Create owner keys for your stake pool 
---
```
jcli key generate --type ed25519 | tee owner.prv | jcli key to-public > owner.pub cat owner.{prv,pub}
```


---
How to update Jormungandr version (from source) with git command
---

| Git something | OSX Terminal Command |
| ------------- | ------------- |
| Load new version  | ``` git checkout v0.8.x ```  |
||```git checkout tags/<latest release tag>```|
| Copy from Github  | ```git pull``` |
| Check submodules  | ```git submodule update --init --recursive```|
| Clean (Optional) |``cargo clean ``|
|Update |``cargo update``|
| Reload Jormungandr old to new | ```cargo install --path jormungandr --force```  |
| Reload jcli old to new |```cargo install --path jcli --force```  |


How to drop all your local changes (and commits), fetch the latest version from the server, and reset your local master branch 
---

| Git something | OSX Terminal Command |
| ------------- | ------------- |
| Load new version  | ``` git fetch origin ```  |
| Reset  | ```git reset --hard origin/master``` |

---
Other tools and commands for reference
---

| Terminal | Command |
| ------------- | ------------- |
| Edit your folder paths for bash commands | ```touch ~/.bash_profile; open ~/.bash_profile```  |
| Edit your config.yaml | ```nano config.yaml```  |
| Find your jormungandr folder location  | ```which jormungandr``` |
| Find your sudo folder | ```which sudo```  |
| Create new folder (directory) |```mkdir <ANY FOLDER NAME>```  |
| Find your mac version |```uname -a```  | 
| Check the file permissions (execute?) |```ls -l```  | 
| Check the hidden files |```ls -a```  | 
|Check for ssh keys|```ls -al ~/.ssh```|
| Edit permission to execute command| ```chmod +x createAddress.sh``` | makes it executable  |
| Edit file permission to execute command | ```chmod +x faucet-send-money.sh``` | makes it executable  |
| Check if you are connecting to nodes | ```netstat -a \| grep ESTABLISHED``` [netstat reference here](https://explainshell.com/explain?cmd=netstat+-an+%7C+grep+%3A80+%7C+wc+-l) | tcp4--macbook-pro.61611lb-192-30-253-11.https ESTABLISHED |
|Check your public key from your account address|```jcli address info <insert account address here>```|
| Check the blockchain statistics | ```jcli rest v0 node stats get -h http://127.0.0.1:3100/api``` | blockRecvCnt: 0BlockDate: "217.36826"  |
|Shutdown a Node (FYI) |```jcli rest v0 shutdown get -h http://127.0.0.1:3100/api```|
| How to stop the command line if | (ctrl)+c |
|ulimit error |```launchctl limit maxfiles``` https://forum.aeternity.com/t/solved-problems-setting-up-a-node-on-osx-mojave/1678 |
|Find your public ip address|```ifconfig```|

List envronment variables
---
  ```
  printenv | more
  ```



Check the node stats
---
```curl http://127.0.0.1:3100/api/v0/node/stats```



>How to download the UTXO list from the blockchain

```
jcli rest v0 utxo get --host "http://127.0.0.1:3100/api" 
```



  

genesis.yaml example
---
```
genesis:
    sig_key: kes25519-12-sk1qqqqqqxrnh4zjh828faxytve8d...really-long-string
    vrf_key: vrf_sk1wtgsdfaluirbcurebpieurvjbrlerjblbrezj
    node_id: 8ca471252c536d55fiuheroiseriuaeaircapeiuhfwe
```
>Format  of the genesis.yaml file
```
genesis:\
  sig_key: Content of stake_pool_kes.prv file \
  vrf_key: Content of stake_pool_vrf.prv file \
  node_id: Content of stake_pool.id file 

```

References
--- 
[IOHK Quickstart reference](https://input-output-hk.github.io/jormungandr/quickstart/01_command_line.html) 


Technical support [Official Help desk](https://iohk.zendesk.com/hc/en-us/articles/360036898153-How-to-install-Jormungandr-Networking-Linux-macOS-) and here:
https://iohk.zendesk.com/hc/en-us

[Cardano StakePool Community on telegram](https://t.me/CardanoStakePoolWorkgroup) and the Github support triage for the Shelley testnet if you have any questions.


Scripts to create addresses and stakepool ids
https://github.com/input-output-hk/jormungandr-qa/tree/master/scripts

Incentivized Testnet Stake Pool Registry
https://github.com/cardano-foundation/incentivized-testnet-stakepool-registry/blob/f9930f7c9eb8b4360472b3890bdaeb0ad1eb7743/README.md




