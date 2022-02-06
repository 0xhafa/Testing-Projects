# Install Cardano Wallet
This will guide to compile and install cardano-wallet into Ubuntu, directly from the source-code. This component provides a CLI (Command Line Interface) and Web API for creating multiple Cardano wallets, sending transactions, getting transaction history details, wallet balances and more!

In this section, we will walk you through the process of downloading, compiling and installing `cardano-wallet` into your Linux / MacOS based operating system.

## Downloading & Compiling
Tnstall stack, if we don't have it:
```shell
curl -sSL https://get.haskellstack.org/ | sh
```

Check the version that has been installed:
```shell
stack --version
```

You should see something like this:
```shell
Version 2.7.3, Git revision 7927a3aec32e2b2e5e4fb5be76d0d50eddcc197f x86_64 hpack-0.34.4
```

Considering you followes the Cardano Node Installation, you should have the `$HOME/cardano-src` directory. If not, let's create a working directory to store the source-code and build for `cardano-wallet`.
```shell
mkdir -p $HOME/cardano-src
cd $HOME/cardano-src
```

Next we download the `cardano-wallet` source-code:
```shell
git clone https://github.com/input-output-hk/cardano-wallet.git 
cd ./cardano-wallet/ 
```

Switch the repository to the latest tagged commit:
```shell
TAG=$(git describe --tags --abbrev=0) && echo latest tag $TAG 
git checkout $TAG
```

## Build and Install the Node
We can now build `cardano-wallet` code to produce executable binaries.
```shell
stack build --test --no-run-tests
```

At this point, I had a problem regarding memory allocation to my WSL instance. In order to overcome this problem I had to migrate the installation to a AWS virtual machine.

Install the newly built `cardano-wallet` binary to the `$HOME/.local/bin` directory:
```shell
stack install
```

Check the version that has been installed:
```shell
cardano-wallet version
```

You should see something like this:
```shell
v2021-11-11 (git revision: dac16ba7e3bf64bf5474497656932fd342c3b720)
```

🎉🎉🎉 You have successfully installed cardano-wallet into your Linux/MacOS system! 🎉🎉🎉