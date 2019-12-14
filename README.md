
# How to load Jormungandr 0.8.X on Mac OSX 
> Dec 14, cliffc2 

* To do list (overview)
  * create github account
  * download vscode
  * load sqlite (if not loaded - for blockchain database)
  * configure .bash_profile and .bashrc
  * if you want to download zip file for osx then    
      * unzip tar file 
      * drag and drop in jormungandr folder goto make 2 folders
  * if you want download from source then 
      * install Rust
      * build jormungandr program from source (using the cargo command)
      * build jcli program from source (using the cargo command)
  * make 2 folders (in terminal or finder)
    * jormungandr (inside home directory folder)
    * storage (inside home directory folder)
  * create a file called config.yaml (in jormungandr folder)
    * edit (copy and paste) your (config.yaml) and 
    * change computer's ip address port number to 3100 
    * find the right genesis block (add to .bashrc)
    * reload .bash_profile (.bashrc)
  * start jormungandr testnet 
  * check the testnet status (is node in 'sync'?) 
  * if you want rewards from the incentivized testnet (itn)
      * if yes - goto cardano-wallet (itn)
        * create a 15 word password
        * create a secret key from 15 word 
      * if no - just create it from a script
        * download script
        * change permission
        * run script
  * if you do not want rewards 
        * create a random secret key
        * public key
        * and account address
  * then get tokens from faucet (or telegram group)
  * check your balance
  * if balance is (+) send test ada tokens to another account address
  * delegate to stakepool
  * create stake pool keys (4)
  * create stake pool certificate from stake pool keys
  * get node id
  * create your owner keys
  * register your stakepool for incentives

  
> IOHK setup references https://iohk.zendesk.com/hc/en-us
  

Download the Jormungandr 0.8.0 OSX zip file
--- 
> View latest releases https://github.com/input-output-hk/jormungandr/releases/ 

* look for jormungandr-v0.8.0-x86_64-apple-darwin.tar.gz 
* download it
* unzip the tar
* open the "home" folder in finder (command+shift+H)
* create a new folder called jormungandr in the home directory
* drag and drop the 2 files (jcli and jormungandr programs) into jormungandr folder

>OR you can type this in the Terminal to make 2 folders

```
mkdir -p ~/jormungandr     
mkdir -p ~/tmp/jormungandr080 
```
>  This [tmp/jormungandr080] is declared in the config.yaml file
---




Create and edit a new file called config.yaml
---

```
cd jormungandr 
nano config-080.yaml
```  

>Copy the config.yaml below and open nano editor (or vscode) config needs to be in jormungandr folder - shortcuts for 
copy (command+c) and paste (command+v) then save (control+o) and close nano (control+x) 


copy this node config for itn_rewards_v1-config.yaml for 0.8.2 (itn)
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
      "blocks": "normal",
      "messages": "low"
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
>Troubleshooting - name your configs according to version so you can see them clearly - this storage is also separated from previous rc versions for refernce reasons

Configure .bash_profile file 
---
>Open the .bash_profile file in nano. The period in .bash_profile denotes the file is hidden in the finder (hold command+shift+[period key] to show hidden files in finder). Note - these are forked from Chris Graffagnino https://gist.github.com/Chris-Graffagnino/4d1be0b88dcaa93440a81dcafdc47afd#create-node-configyaml



```
nano ~/.bash_profile
```
>Copy and paste the following into the .bash_profile file
```
export ARCHFLAGS="-arch x86_64"
test -f ~/.bashrc && source ~/.bashrc

if [ $USER = $USERNAME ]; then
  if [ $SHELL = "/bin/bash" ]; then
    ulimit -n 4096
  fi
fi

function start_node() {
    GREEN=$(printf "\033[0;32m")
    nohup jormungandr --config node-config.yaml --genesis-block-hash $GENESIS_BLOCK_HASH >> logs/node.out 2>&1 &
    echo ${GREEN}$(ps | grep jormungandr)
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

function memory() {
    top -o %MEM
}

function nodes() {
    nodes="$(netstat -tupan | grep jor | grep EST | cut -c 1-80)"
    total="$(netstat -tupan | grep jor | grep EST | cut -c 1-80 | wc -l)"
    printf "%s\n" "${nodes}" "----------" "Total:" "${total}"
}

function num_open_files() {
    echo "How many open files?"
    echo "$(lsof -u $(whoami) | wc -l)"
}

function is_pool_visible() {
    echo ${GREEN}$(jcli rest v0 stake-pools get --host "http://127.0.0.1:${REST_PORT}/api" | grep $(cat stake_pool.id))
}

function create_stake_pool() {
    echo "$(./createStakePool.sh ${REST_PORT} $(cat receiver_secret.key))"
}

function delegate_account() {
    echo "$(./delegate-account.sh $(cat stake_pool.id) ${REST_PORT} $(cat receiver_secret.key))"
}

function start_leader_node() {
    GREEN=$(printf "\033[0;32m")
    nohup jormungandr --config node-config.yaml --secret node_secret.yaml --genesis-block-hash ${GENESIS_BLOCK_HASH} >> logs/node.out 2>&1 &
    echo "${GREEN}$(ps | grep jormungandr)"
}

function leader_logs() {
    echo "Has this node been scheduled to be leader?"
    echo "$(jcli rest v0 leaders logs get -h http://127.0.0.1:${REST_PORT}/api)"
}
```
---

Configure .bashrc
---
```
nano ~/.bashrc
``` 
>Copy and paste the following into .bashrc

```
# ~/.bashrc: executed by bash(1) for non-login shells.
# see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
# for examples

# If not running interactively, don't do anything
case $- in
    *i*) ;;
      *) return;;
esac

# don't put duplicate lines or lines starting with space in the history.
# See bash(1) for more options
HISTCONTROL=ignoreboth

# append to the history file, don't overwrite it
shopt -s histappend

# for setting history length see HISTSIZE and HISTFILESIZE in bash(1)
HISTSIZE=1000
HISTFILESIZE=2000

# check the window size after each command and, if necessary,
# update the values of LINES and COLUMNS.
shopt -s checkwinsize

# If set, the pattern "**" used in a pathname expansion context will
# match all files and zero or more directories and subdirectories.
#shopt -s globstar

# make less more friendly for non-text input files, see lesspipe(1)
[ -x /usr/bin/lesspipe ] && eval "$(SHELL=/bin/sh lesspipe)"

# set variable identifying the chroot you work in (used in the prompt below)
if [ -z "${debian_chroot:-}" ] && [ -r /etc/debian_chroot ]; then
    debian_chroot=$(cat /etc/debian_chroot)
fi

# set a fancy prompt (non-color, unless we know we "want" color)
case "$TERM" in
    xterm-color|*-256color) color_prompt=yes;;
esac

# uncomment for a colored prompt, if the terminal has the capability; turned
# off by default to not distract the user: the focus in a terminal window
# should be on the output of commands, not on the prompt
#force_color_prompt=yes

if [ -n "$force_color_prompt" ]; then
    if [ -x /usr/bin/tput ] && tput setaf 1 >&/dev/null; then
        # We have color support; assume it's compliant with Ecma-48
        # (ISO/IEC-6429). (Lack of such support is extremely rare, and such
        # a case would tend to support setf rather than setaf.)
        color_prompt=yes
    else
        color_prompt=
    fi
fi

if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
fi
unset color_prompt force_color_prompt

# If this is an xterm set the title to user@host:dir
case "$TERM" in
xterm*|rxvt*)
    PS1="\[\e]0;${debian_chroot:+($debian_chroot)}\u@\h: \w\a\]$PS1"
    ;;
*)
    ;;
esac

# enable color support of ls and also add handy aliases
if [ -x /usr/bin/dircolors ]; then
    test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
    alias ls='ls --color=auto'
    #alias dir='dir --color=auto'
    #alias vdir='vdir --color=auto'

    alias grep='grep --color=auto'
    alias fgrep='fgrep --color=auto'
    alias egrep='egrep --color=auto'
fi

# colored GCC warnings and errors
#export GCC_COLORS='error=01;31:warning=01;35:note=01;36:caret=01;32:locus=01:quote=01'

# colored vars
GREEN=$(printf "\033[0;32m")


# Add an "alert" alias for long running commands.  Use like so:
#   sleep 10; alert
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'

# Alias definitions.
# You may want to put all your additions into a separate file like
# ~/.bash_aliases, instead of adding them here directly.
# See /usr/share/doc/bash-doc/examples in the bash-doc package.

if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi

# enable programmable completion features (you don't need to enable
# this, if it's already enabled in /etc/bash.bashrc and /etc/profile
# sources /etc/bash.bashrc).
if ! shopt -oq posix; then
  if [ -f /usr/share/bash-completion/bash_completion ]; then
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi

function parse_git_branch() {
    BRANCH=`git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/\1/'`
    if [ ! "${BRANCH}" == "" ]
    then
        echo "(${BRANCH})"
    else
        echo ""
    fi
}

# Comment the following line if you prefer a prompt *not* include git branch info
export PS1="\[\e[36m\]\w\[\e[m\]\[\e[35m\] \`parse_git_branch\`\[\e[m\] \[\e[36m\]:\[\e[m\] "

export PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin:/usr/bin/python3"
```

Type each of the following commands in terminal
---
> forked from Chris Graffagnino Linux setup https://gist.github.com/Chris-Graffagnino/4d1be0b88dcaa93440a81dcafdc47afd#type-each-of-the-following-commands-in-terminal
```
echo "export USERNAME='<YOUR USERNAME>'" >> ~/.bashrc
```
> note - the terminal prompt has `<YOUR USERNAME>` or you can list the users with the command ```ls /users ```
```
echo "export PUBLIC_IP_ADDR='<YOUR PUBLIC IP ADDRESS>'" >> ~/.bashrc
```
> you can find the `<YOUR PUBLIC IP ADDRESS>` inet 192.168.43.102 with the command ```ifconfig``` look for the number set after "inet" 
```
echo "export REST_PORT='3100'" >> ~/.bashrc
```
> This is the port number in the config.yaml - 3100  
```
echo "export JORMUNGANDR_STORAGE_DIR='/tmp/jormungandr080'" >> ~/.bashrc
```
> this matches the storage location in the config.yaml  
```
echo "export GENESIS_BLOCK_HASH='<BETA OR NIGHTLY GENESIS BLOCK HASH>'" >> ~/.bashrc
```
> Genesis block hash for 0.8.0 nightly `65a9b15f82619fffd5a7571fdbf973a18480e9acf1d2fddeb606ebb53ecca839`

>Genesis block hash for 0.7.0 beta
`27668e95121566df0bb2e2c11c5fd95dfe59efd570f8f592235ecff167ca3f29`


> What did we just do?
"echo" essentially means "print to screen"
"export" declares a variable in a special way, so that any shells that spawn from it inherit the variable.">>" means "take the output of the previous command and append it to the end of a file (.bashrc, in this case)

Load the new .bash_profile and .bashrc 
---
```
source ~/.bash_profile
```

>Source config files make these new variables available in the terminal. FYI There's a command in .bash_profile that sources .bashrc 

```
 place holder
```


>Troubleshooting note - open a new terminal (command+N)




---
Start the Jormungandr Node 0.8.0 (passive mode)
---
```
jormungandr --genesis-block-hash 65a9b15f82619fffd5a7571fdbf973a18480e9acf1d2fddeb606ebb53ecca839 --config config.yaml 
```


>Troubleshooting note: check for the latest genesis block hash and config.yaml path. Also check your ports to make sure they are calling the right number. Check your ip address and port (127.0.0.1:3100) 

---

Check the node - is it in 'sync'?
---
> Open a new Terminal > shell > new window (command+N)

 ```jcli rest v0 node stats get -h http://127.0.0.1:3100/api``` 

>Check the node stats (same as jcli rest v0 node stats command)

```curl http://127.0.0.1:3100/api/v0/node/stats```

---

Delegate your tokens to a stakepool
---
>If you are not operating a stakepool then delegate
https://github.com/input-output-hk/jormungandr-qa/tree/master/scripts 

``` delegate-account.sh <STAKE_POOL_ID> <REST-LISTEN-PORT> <ACCOUNT-SK> ```

> View the stakepools in your browser when running

 ```http://127.0.0.1:3100/api/v0/stake_pools```


  




Check your fee settings
---
```jcli rest v0 settings get -h http://127.0.0.1:3100/api``` 



---
Create your account address 
---
>Download this ```createAddress.sh``` script (not recognized by Daedalus or Yoroi)

```
curl -sLOJ https://raw.githubusercontent.com/input-output-hk/jormungandr-qa/master/scripts/createAddress.sh
```

> You can find the script here https://github.com/input-output-hk/jormungandr-qa/blob/master/scripts/createStakePool.sh


>Change permissions (make executable +x)

```
chmod +x createAddress.sh
```

>Run the create address script

```
createAddress.sh account
```

---

Get tokens (testnet ADA from telegram group if faucet is out)
---

 https://testnet.iohkdev.io/en/cardano/shelley/tools/faucet/

 ---
 
Check your account address to see your token balance 
---
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

> Create or download send-lovelaces.sh script the make the file executable 

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

* To do list (load from source code)
  * install the [Rust programming language](https://github.com/rust-lang.)
  * load jormungandr program (using the cargo command)
  * load jcli (using the cargo command)
  * load sqlite (if not loaded - for blockchain database)
  * edit port and check ip address
  * make 2 folders (for testnet program and blockchain database)
  * edit config.yaml (computer's node info - ip address and port)
  * start jormungadr node 
  * check node is in 'sync'
  * create secret keys 
  * create public keys from secret keys 
  * create account address from public keys 
  * get tokens from faucet
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
Download Jormungandr 
---

``` 
git clone --recurse-submodules https://github.com/input-output-hk/jormungandr 
```

>Enter the Jormungandr folder

 ```
 cd jormungandr
 ``` 

Load jormungandr 
---
 ```
 cargo install --path jormungandr
 ```

Load jcli 
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


# Start Jormungandr 


```
jormungandr --genesis-block-hash 65a9b15f82619fffd5a7571fdbf973a18480e9acf1d2fddeb606ebb53ecca839 --config config.yaml 
```
>Check to see if node is receiving blocks
```
jcli rest v0 node stats get -h http://127.0.0.1:3100/api
```
> You can also view this in firefox
```
curl http://127.0.0.1:3100/api/v0/node/stats
```




> Now we can make keys with this script here  https://github.com/input-output-hk/jormungandr-qa/tree/master/scripts

```
createAddress.sh account
```

# Create your keys and address (from scratch)



>To be recognized by Daedalus and Yoroi you need to get a secret key from the cardano-wallet 

 https://github.com/input-output-hk/cardano-wallet/wiki/Wallet-command-line-interface

--- 

> Create a secret key 

 ```
 jcli key generate --type=Ed25519Extended > receiver_secret.key
 ```

>Make a public key from the secret key 

```
cat receiver_secret.key | jcli key to-public > receiver_public.key
``` 

> Make an account address from the public key 

```
jcli address account --prefix addr --testing $(cat receiver_public.key) | tee receiver_account.txt
``` 

> View the account address

```
cat receiver_account.txt
```


>List files and folders in jormungandr

```
ls 
``` 

> You should see receiver_account.txt, receiver_secret.key, and a receiver_public.key 
IT IS EXTREMELY IMPORTANT THAT YOU SAVE YOUR KEYS FOR FUTURE USE
---

Get testnet ADA tokens (test-ADA)
---
 https://testnet.iohkdev.io/en/cardano/shelley/tools/faucet/
```
curl -X POST https://faucet.faucet.jormungandr-testnet.iohkdev.io/send-money/$(cat ~/files/receiver_account.txt)
```
---
Check your account address to see your token balance 
---
```
jcli rest v0 account get $(cat receiver_account.txt) -h  http://127.0.0.1:3100/api
``` 
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




 ---
Incentivized Testnet Stake Pool Registry
---
> https://github.com/cardano-foundation/incentivized-testnet-stakepool-registry/blob/f9930f7c9eb8b4360472b3890bdaeb0ad1eb7743/README.md

Create owner keys for your stake pool 
---
```jcli key generate --type ed25519 | tee owner.prv | jcli key to-public > owner.pub cat owner.{prv,pub}```

---

Create a Stake pool (key and cert) by hand
---

|   | OSX Terminal |
| ------------- | ------------- |
| Make a secret vrf key  | ```        jcli key generate --type=Curve25519_2HashDH > stake_pool_vrf.prv         ```  |
| Make a public vrf key from secret vrf key  | ```cat stake_pool_vrf.prv jcli key to-public > stake_pool_vrf.pub```  |
| Make a secret stakepool key from scratch  | ```jcli key generate --type=SumEd25519_12 > stake_pool_kes.prv``` |
| Make a public stakepool key from secret | ```cat stake_pool_kes.prv jcli key to-public > stake_pool_kes.pub``` |

Get your stake pool cert 
---

>This is the format with indentions for reference
    
    jcli certificate new stake-pool-registration \
    --kes-key $(cat stake_pool_kes.pub) \
    --vrf-key $(cat stake_pool_vrf.pub) \
    --start-validity 0 \
    --management-threshold 1 \
    --tax-fixed 1000000 \
    --tax-limit 1000000000 \
    --tax-ratio "1/10" \
    --owner $(cat owner_key.pub) > stake_pool.cert


How to update Jormungandr version (from source) with git
---

| Git something | OSX Terminal Command |
| ------------- | ------------- |
| Load new version  | ``` git checkout v0.x.x ```  |
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


Other tools and commands
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
|Run debug |```jormungandr --genesis-block-hash check4thelatestgenesisblock3 --config config.yaml --log-level=debug``` |
|Find your public ip address|```ifconfig```|

List envronment variables
---
  ```
  printenv | more
  ```



Check the node statistics
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

The Official reference 
[IOHK Quickstart reference](https://input-output-hk.github.io/jormungandr/quickstart/01_command_line.html) 
---

Technical support [Official Help desk](https://iohk.zendesk.com/hc/en-us/articles/360036898153-How-to-install-Jormungandr-Networking-Linux-macOS-) and here:
https://iohk.zendesk.com/hc/en-us

[Cardano StakePool Community on telegram](https://t.me/CardanoStakePoolWorkgroup) and the Github support triage for the Shelley testnet if you have any questions.


Scripts to create addresses and stakepool ids
https://github.com/input-output-hk/jormungandr-qa/tree/master/scripts

Incentivized Testnet Stake Pool Registry
https://github.com/cardano-foundation/incentivized-testnet-stakepool-registry/blob/f9930f7c9eb8b4360472b3890bdaeb0ad1eb7743/README.md


Cardano-wallet (to be recognized by Daedalus and Yoroi you need to get a secret key from the cardano-wallet)
https://github.com/input-output-hk/cardano-wallet/wiki/Wallet-command-line-interface

