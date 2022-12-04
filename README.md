# Writing Beginner Smart Contracts on Solana with Rust and Moralis

## Configuring the Devolopment Environment

0. Before we can write or deploy a smart contract to Solana, we need to set up our development environment. This tutorial implies that you are using a unix (MacOS or Linux) based machine. 

    - Our first step is to install rust, the native programming language and suite of tools that Solana utilizes. Rust can be installed by running the following “curl” command in your terminal: `curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`
        - A prompt will appear asking you to choose between options `1. proceed with installation`, `2. customize installation` and `3. cancel installation`. Choose option 1 and press return. 

    - Next we will need to install the Solana CLI. Paste the following into your terminal to install the latest version: `sh -c "$(curl -sSfL https://release.solana.com/stable/install)"` 
        - After the installation is complete, run `source "$HOME/.cargo/env"` to configure Solana and Rust as environment varibles in your shell. 

    - We now need to setup a local directory to keep things simple and organised. In the terminal run: `mkdir ~/testnet-solana-wallet` to create a new folder in the home directory.
        - Next, run `solana-keygen new --outfile ~/testnet-solana-wallet/my-keypair.json` to create a new solana wallet. The CLI will prompt you for a password, but for purposes of testing this is best left blank. The output will be the seed phrase, which you should record in a safe place.
 
    - Our next step is to point Solana at the devnet cluster. To do this we execute the command `solana config set --url https://api.devnet.solana.com` in the terminal. 
        -  We can now create a ID config file for our wallet by executing `solana-keygen new -o /Users/nickcarp/.config/solana/id.json` in the terminal. 
        -  Finally, we can now fund our wallet though the devnet faucet by running thr command `solana airdrop 1`

1. Next we can proceed with writing and deploying the smart contract. You can choose any IDE for this portion, but I chose Visual Studio Code as it is what the vast majority of users are familiar with. 
    - With visual studio open, navigate to the home directory and find the `testnet-solana-wallet` folder. 
2. Open a new terminal by navigating the VS Code taskbar and selecting `Terminal` -> `New Terminal`
3. In your new VS Code terminal execute the following command: `cargo init hello_world --lib`. This command builds a new local directory called `hello_world`. 
    - Let's choose this as our working directory by typing `cd hello_world` into our terminal. 
4.  Now, in the editor part of VS Code, lets open the file `Cargo.toml`. Add the following file below the `[dependancies]` tag in the `.toml` file. Be sure to save the file after!
```
[lib]
name = "hello_world"
crate-type = ["cdylib", "lib"]
```
5. Nextly we will add the `solana_program` package to our CLI by running `cargo add solana_program` in the terminal. (This can take multupile tries to get a "happy" response from the crates.io server. If the download stalls out press `ctrl + c` and re-run the command. 
6. Next navigate to the file located in the directory at `src/lib.rs`. We will paste the code below to replace the contents of the file. Be sure to save the file!
```
use solana_program::{
    account_info::AccountInfo,
    entrypoint,
    entrypoint::ProgramResult,
    pubkey::Pubkey,
    msg,
};

entrypoint!(process_instruction);

pub fn process_instruction(
    program_id: &Pubkey,
    accounts: &[AccountInfo],
    instruction_data: &[u8]
) -> ProgramResult {

    msg!("Hello, world!");

    Ok(())
}
```
7. Now that our contract is written, we need to compile it via cargo and our terminal by running `cargo build-bpf`.
8. Now that our contract is compiled, its time to deploy it to devnet by running: `solana program deploy ./target/deploy/hello_world.so`. Be sure to record the program ID somewhere safe.


  
