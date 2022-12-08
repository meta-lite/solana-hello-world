# Writing Beginner Smart Contracts on Solana with Rust and Moralis

## Configuring the Development Environment

0. Before we can write or deploy a smart contract to Solana, we need to set up our development environment. This tutorial implies that you are using a unix (MacOS or Linux) based machine. It is also implied that you have a package manager installed (brew for MacOS / apt for Linux). This tutorial will use brew but any brew command can be executed on Linux by replacing `brew` with `sudo apt` in your command. 

    - Let's make sure our dependencies are in working order. Run the commands `brew install nvm`, `brew install yarn` and `brew install node` in succession. Follow any additional installation steps. Also ensure you have a fresh installation of Phantom Wallet in your preferred browser. (https://phantom.app/)     

    - Our first step is to install rust, the native programming language and suite of tools that Solana utilizes. Rust can be installed by running the following “curl” command in your terminal: `curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`
        - A prompt will appear asking you to choose between options `1. proceed with installation`, `2. customize installation` and `3. cancel installation`. Choose option 1 and press return. 

    - Next we will need to install the Solana CLI. Paste the following into your terminal to install the latest version: `sh -c "$(curl -sSfL https://release.solana.com/stable/install)"` 
        - After the installation is complete, run `source "$HOME/.cargo/env"` to configure Solana and Rust as environment variables in your shell. 

    - We now need to setup a local directory to keep things simple and organized. In the terminal run: `mkdir ~/testnet-solana-wallet` to create a new folder in the home directory.
        - Next, run `solana-keygen new --outfile ~/testnet-solana-wallet/my-keypair.json` to create a new solana wallet. The CLI will prompt you for a password, but for purposes of testing this is best left blank. The output will be the seed phrase, which you should record in a safe place. You should also import this seed phrase into your fresh Phantom Wallet extension by clicking "Import Wallet".
 
    - Our next step is to point Solana at the devnet cluster. To do this we execute the command `solana config set --url https://api.devnet.solana.com` in the terminal. 
        -  We can now create a ID config file for our wallet by executing `solana-keygen new -o /Users/USER/.config/solana/id.json` in the terminal, replacing USER with your local login. 
        -  Finally, we can now fund our wallet though the devnet faucet by running the command `solana airdrop 1`

1. Next we can proceed with writing and deploying the smart contract. You can choose any IDE for this portion, but I chose Visual Studio Code as it is what the vast majority of users are familiar with. 
    - With visual studio open, navigate to the home directory and find the `testnet-solana-wallet` folder. 
2. Open a new terminal by navigating the VS Code taskbar and selecting `Terminal` -> `New Terminal`
3. In your new VS Code terminal execute the following command: `cargo init hello_world --lib`. This command builds a new local directory called `hello_world`. 
    - Let's choose this as our working directory by typing `cd hello_world` into our terminal. 
4.  Now, in the editor part of VS Code, let's open the file `Cargo.toml`. Add the following file below the `[dependencies]` tag in the `.toml` file. Be sure to save the file after!
```
[lib]
name = "hello_world"
crate-type = ["cdylib", "lib"]
```
5. Nextly we will add the `solana_program` package to our CLI by running `cargo add solana_program` in the terminal. (This can take multiple tries to get a "happy" response from the crates.io server. If the download stalls out press `ctrl + c` and re-run the command. 
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
8. Now that our contract is compiled, it's time to deploy it to devnet by running: `solana program deploy ./target/deploy/hello_world.so`. Be sure to record the program ID somewhere safe.
9. We will be using the following git repo to test our newly deployed contract: https://github.com/johnvsnagendra/solana-smart-contract-helloWorld. Be sure to open it up in a new tab. 
    - From the Github repo, navigate to the bright green "Code" button. Click the button to display the git link. Press the copy button. 
    - In our terminal, run the command `git clone <LINK>` replacing the <> and their contents with the link you just copied from Github. This will pull the contents of this repo into our hello_world directory. There are a couple of edits we need to make before proceeding: 
        - We must first rename the file `.env.local.example` to `.env.local`. 
        - Create an account at https://moralis.io for free to generate an API key. After creating an account and completing the mostly self-explanatory "onboarding" process, we can retrieve the API Key. From the page displayed after onboarding, click the "Web3 APIs" button and copy the key. 
        - Navigate back to `.env.local` and add the API key immediately after the `MORALIS_API_KEY=` variable declaration. Be sure to save the file!
10. As we approach the end of our API setup, we need to add the program ID that we recorded earlier to the `/src/components/templates/helloWorld/HelloWorld.tsx` file located from our `hello_world` directory. Line 19 should display `const programId = '3Adih9H8CheKTKfmmUYtr8cksbwoxvhWzsdupK6MfJAX';`. Replace everything between the single quotation marks with our recorded program ID. 
    - We will also need to establish a connection with the browser in our local host instance. Run the command `npm install --save @solana/web3.js` in our working directory to download the required javascript file. This should be the last component we need to launch our frontend interface and connect our backend smart contract. 
11. From our terminal run the commands `cd solana-dapp-helloWorld` and `yarn install` to build our frontend application.
    - Once our program is built, we can run the command `yarn run dev` to spin up our local web server. This can be accessed at http://localhost:3000 in the browser where we setup our fresh Phantom extension. This will launch the `ethereum-boilerplate` frontend, modified to interact with phantom. Switch your Phantom wallet to devnet by clicking `Phantom Wallet` -> `W1` -> `Developer Settings` -> `Change Network` -> `Devnet`. You should see the balance we airdropped from the CLI earlier. If you don't, fund your wallet by typing your address into https://solfaucet.com/.
    - You should see a page title that says "Ethereum Boilerplate (Solana Version)". Click the "Hello World" tab on the menu strip at the top of the page to go to the contract testing screen. Click "Select Wallet" in the top right corner of the page, select Phantom Wallet and approve the connection in the extension. Once connected, Click the "Run Program" button and sign the transaction in the Phantom extension. If you were successful, the transaction should execute and display the output: `Program log: Hello, world!`. Congratulations! You just built your first Solana program!
12. It is good practice to disconnect your wallet and close out of the localhost server. Click your wallet address in the top right corner and click "Disconnect". Now enter your terminal in VS Code and press `ctrl + c`. 
13. You can use this framework to test any future smart contract you may write in rust with some slight frontend tweaks. Here's an example contract I made that displays a meatball recipe:  
```
 use solana_sdk::{
    account::Account,
    entrypoint,
    info,
    program_error::ProgramError,
    pubkey::Pubkey,
    system_program,
    sysvar::clock::Clock,
};

#[entrypoint]
fn main(account: &Account, _info: &Info, _clock:  &Clock) -> Result<(), ProgramError> {
    info!("Meatballs and Pasta Recipe:");
    info!("Ingredients:");
    info!("- 1 lb ground beef");
    info!("- 1/2 cup bread crumbs");
    info!("- 1/4 cup grated Parmesan cheese");
    info!("- 1/4 cup milk");
    info!("- 1 egg");
    info!("- 1 clove garlic, minced");
    info!("- 1/2 teaspoon salt");
    info!("- 1/4 teaspoon black pepper");
    info!("- 1/4 teaspoon dried oregano");
    info!("- 1/4 teaspoon dried basil");
    info!("- 1/4 teaspoon dried parsley");
    info!("- 1/2 cup olive oil");
    info!("- 2 cups tomato sauce");
    info!("- 8 oz pasta");
    info!("Instructions:");
    info!("1. In a large bowl, combine the ground beef, bread crumbs, Parmesan cheese, milk, egg, garlic, salt, pepper, oregano, basil, and parsley. Mix well to combine.");
    info!("2. Roll the mixture into balls about the size of a golf ball and set aside.");
    info!("3. Heat the olive oil in a large skillet over medium heat. Add the meatballs and cook, turning occasionally, until browned on all sides.");
    info!("4. Stir in the tomato sauce and bring to a simmer. Reduce the heat to low and simmer, covered, for 15 minutes, or until the meatballs are cooked through.");
    info!("5. Meanwhile, cook the pasta according to the package instructions.");
    info!("6. Serve the meatballs and sauce over the cooked pasta.");

    Ok(())
}
```
