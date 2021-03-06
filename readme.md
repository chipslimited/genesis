# Genesis Block
The genesis block generator converts the `snapshot.csv` file committed to this directory into a `genesis.json` required by and compatible with **cpsd**

## Usage
1. Visit [CPS Genesis Block Generator](http://cpsio.github.io/genesis/genesis.html)
2. Wait for button to appear and then click **genesis.json** to download


## What it does
1. Loads `snapshot.csv` from this repository (when/if available)
2. Converts into well-formed `genesis.json` file

# Snapshot Generator
**Attention: This generator should not be used to generate a testnet genesis.json due to liquid tokens and susceptibility to divergence, it is provided for experimentation and verification purposes**

Simple web interface based on web3js based script that generates a token distribution list in CSV format, several other CSVs for verification and full console logging with log download. 

## Prerequisites
- A Local Ethereum Node, **parity** suggested
- A modern browser
- **Turn off** MetaMask if you have it running
- Python or means to run simple webserver

## Compatibility 
Tested full-size snapshot successfully with varying performance results:
- Chrome (Good Performance)
- Brave (Good Performance)
- Firefox (Inferior Performance, turn off logging)
- Safari (Terrible Performance, turn off logging and go read a book)
- Explorer/Edge, untested

_Note:_ All outputs were 100% identical despite performance degradation and varying error catches and retries.

## Usage

### On Github.io (Github Pages)
1. Navigate to http://cpsio.github.io/genesis/snapshot.html
1. Start your parity or geth node with flag `--rpccorsdomain 'http://cpsio.github.io'`
1. Open developer console for some information, `debug.output()` will give you some calculations
1. When complete, links to download the snapshot, other datasets and convenience genesis.json will be made available.

### On Your Machine
1. Checkout or download this repository
1. Navigate to the project directory
1. Start a web server. For example, you could run `python -m SimpleHTTPServer 8000` in the project directory to start a webserver at **http://localhost:8000**.
1. Start your parity or geth node with flag `--rpccorsdomain 'http://localhost:8000'` ... If you didn't use port `8000`, change accordingly. 
1. Navigate to [http://localhost:8000](http://localhost:8000) (if you used a different port, adjust accordingly)
1. Open developer console for some information, `debug.output()` will give you some calculations
1. When complete, links to download the snapshot and other datasets will be made available.

## Data Exports
- CSV / JSON
    - **Snapshot**
    - Rejects
    - Unclaimed-Claimable CPS TX
    - Claimed CPS TX
- Log
    - Full output log. 
- **genesis.json**
    - Convenience method outputs an additional genesis.json

## Notes
1. Script can run in unfocused tab.
1. Don't do anything crazy in your browser while running this script.
1. If the script hits an error (shouldn't) it will attempt to restart itself. 

## Config

There are configurations in `src/config.js`

### User Config

1. `NODE` Change according to your local node configuration (default: http://localhost:8345)
1. `CONSOLE_LOGGING` Will output logs snapshot into developer console, turn off if you want better performance (default:true)
1. `OUTPUT_LOGGING` Generate a downloadable snapshot log (default:false)
1. `VERBOSE_REGISTRY_LOGS` Outputs every single registrant into developer logs, very slow but works in Chrome. Will break firefox. (default:false)


### Snapshot

The snapshot requires a range for output consistency

1. `SS_FIRST_BLOCK` Presently set to block *3904416* or the block with first transaction to the crowdsale contract. 
2. `SS_LAST_BLOCK` The last block to account for in snapshot [TBA]
3. `SNAPSHOT_ENV` Determines filename output.

## Development
- run `npm install`
- If you make changes to scripts, run `npm run compile`
- Please use ES6 conventions



## What it does
1. Compiles the ethereum address registry based on the _LogRegister_ contract event logs.
1. Compiles a list of tokens accidentally sent to the _token_ and _crowdsale_ contracts using _Transfer_ event logs, referred to as "Reclaimable Token TXs"
1. For each registrant, it then...
  - Retrieves CPS public key from `keys [map]`  in CPS Crowdsale contract (Important Note: This relies on contract state instead of inferring state from _LogRegister_)
  - Attempts to fix key if there appears to be an correctable mistake.
  - Calculates the total balance...
     - Obtains the wallet balance associated to the registrant's ethereum address.
     - Obtains the unclaimed token balanace in crowdsale contract associated to the registrant's ethereum address.
     - Checks to see if the current ethereum address has any reclaimable TXs. 
     - Adds them all together
     - Converts from gwei
1. Validates the entry and if it validates, it's added to the distribution.
1. If the registrant doesn't validate due to a zero balance or invalid CPS key, registrant is accessible from _rejects_ CSV output.

## Why a web app?
Interfacing with ethereum ABI and especially a contract's event logs with vanilla ethereum libraries is no simple task. The most well-maintained library that simplifies ABI patterns and generates a reliable output is **Web3js**. Bespoke ABI business logic would increase possiblity of error and ultimately make it far more difficult to verify the result. Web isn't ideal, but it allows for us to focus on the snapshot logic instead of questioning experimental JSON-RPC interface logic or other manually exporting data from centralized source and processing data in multiple steps. 
_From [EthDocs.com](http://www.ethdocs.org/en/latest/contracts-and-transactions/accessing-contracts-and-transactions.html#web3-js):_
> **...using the JSON-RPC interface can be quite tedious and error-prone**, especially when we have to deal with the ABI. Web3.js is a javascript library that works on top of the Ethereum RPC interface. Its goal is to provide a more user friendly interface and **reducing the chance for errors.**