# P2P Betting DApp

This repository contains a Next.js frontend and a Brownie/Solidity smart-contract project for a peer-to-peer sports-betting prototype. The latest commit on `main` is dated December 2, 2023. This README describes the checked-in code; it does not verify that any deployment, API credential, or oracle integration is currently operational.

## What the repository contains

- `src/`: Next.js 12 frontend using React, RainbowKit, wagmi, and ethers.
- `smart-contract/`: Solidity contract, Brownie configuration, and a deployment script.
- The contract defines bet publishing, acceptance, cancellation, bet queries, and a Chainlink request callback.
- The UI contains screens for publishing bets, viewing/accepting available bets, and viewing/canceling a user's published bets. The source should be treated as a prototype; these screens are not evidence of a verified live workflow.

## Run the frontend

Install and run the app from the directory that contains its `package.json`:

```bash
git clone https://github.com/faryann/P2P_Betting_DApp.git
cd P2P_Betting_DApp/src
npm install
npm run dev
```

Then open `http://localhost:3000`. The scripts declared in `src/package.json` also include `npm run build`, `npm start`, and `npm run lint`.

The wallet setup in `src/pages/_app.tsx` lists Polygon Mumbai and Goerli, but the contract reads in the UI specify chain ID `80001` (Polygon Mumbai). The UI hard-codes the contract address `0x67206ddFf1B347aBdFa02eC3B703145930344977`. The explorer link on the landing page points to a different address, `0xF803daD617584A498bc1172f9502BA6867Ad0093`. Neither address should be treated as a verified deployment based on this repository.

The publish screen fetches a fixed 2023 NBA schedule from SportsData.io and filters for games involving Chicago (`CHI`). The schedule URL and API credential are hard-coded in the frontend; the contract also contains a hard-coded SportsData.io request. Replace exposed credentials and verify the API configuration before relying on this integration.

## Smart-contract deployment

The only deployment script is `smart-contract/scripts/deploy.py`. From the repository root, its invocation is:

```bash
cd smart-contract
brownie run scripts/deploy.py --network YOUR_CONFIGURED_NETWORK
```

`YOUR_CONFIGURED_NETWORK` is a placeholder: the repository's `brownie-config.yaml` declares dependencies and compiler remappings, but no network or account configuration. The script loads a Brownie account named `dev1`, deploys `BetContract`, then attempts to transfer 3 LINK to it. The account therefore needs to be configured and funded with the network's native currency and the required LINK token.

The contract constructor hard-codes Chainlink token, oracle, and job settings associated with the project's Mumbai-era setup. The deployment script does not update `src/abi.json` or the frontend's hard-coded contract address. A deployment to another network, or a new deployment address, will require the frontend configuration and ABI to be checked and updated separately.

## Oracle and result handling

The repository does not contain an external-adapter implementation, Chainlink node/job configuration, or Chainlink Automation registration instructions. `BetContract.sol` sends a request to a fixed endpoint using hard-coded oracle settings. Its public `upkeep_setWinner()` function processes the result values already stored in the contract and then requests data again; the repository does not configure an automatic caller for this function. As a result, the code here does not establish a reproducible, automatic, or currently working bet-settlement workflow. No such deployment or oracle steps are documented as verified.

The checked-in `smart-contract/tests/test_bet.py` also does not provide a reliable smoke test: its `publishBet` call does not match the contract's function signature, and its acceptance test treats returned bet structs as IDs. This README does not claim that the tests pass.

## License

This project is licensed under the MIT License; see [LICENSE](LICENSE).
