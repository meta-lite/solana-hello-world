# Writing Beginner Smart Contracts on Solana with Rust and Moralis

## Configuring the Devolopment Environment

0.	Before we can write or deploy a smart contract to Solana, we need to set up our development environment. This tutorial implies that you are using a unix (MacOS or Linux) based machine. 
    -	Our first step is to install rust, the native programming language and suite of tools that Solana utilizes. Rust can be installed by running the following “curl” command in your terminal: `curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`
        -   A prompt will appear asking you to choose between options `1. proceed with installation`, `2. customize installation` and `3. cancel installation`. Choose option 1 and press return. 
    -   Next we will need to install the Solana CLI. Paste the following into your terminal to install the latest version: `sh -c "$(curl -sSfL https://release.solana.com/stable/install)"` 
        - After the installation is complete, run `source "$HOME/.cargo/env"` to configure Solana and Rust as environment varibles in your shell. 
    - We now need to setup a local directory to keep things simple and organised. In the terminal run: `mkdir ~/testnet-solana-wallet` to create a new folder in the home directory.
        - Next, run `solana-keygen new --outfile ~/testnet-solana-wallet/my-keypair.json` 

  
