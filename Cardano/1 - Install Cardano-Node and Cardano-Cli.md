# Install Cardano Node and Cardano Cli
This guide will show you how to compile and install the cardano-node and cardano-cli into your operating system of choice, directly from the source-code.

This tutorial was executed on Ubuntu via Windows Subsystem for Linux (WSL), following the steps of Cardano Documentation (https://developers.cardano.org/docs/get-started/installing-cardano-node/)

## Prerequisites:
To set up the components, you will need:

* Windows, MacOS, or Linux for your operating system
* An Intel or AMD x86 processor with two or more cores, at 1.6GHz or faster (2GHz or faster for a stake pool or relay)
* 12GB of RAM and at least 30GB of free disk space

On Linux, to download the source code and build it, you need the following packages and tools on your Linux system:

* the version control system git,
* the gcc C-compiler,
* C++ support for gcc,
* developer libraries for the arbitrary precision library gmp,
* developer libraries for the compression library zlib,
* developer libraries for systemd,
* developer libraries for ncurses,
* ncurses compatibility libraries,
* the Haskell build tool cabal,
* the GHC Haskell compiler (version 8.10.7 or above).

To install all prerequisites run:
```console
sudo apt-get update -y && sudo apt-get upgrade -y
sudo apt-get install automake build-essential pkg-config libffi-dev libgmp-dev libssl-dev libtinfo-dev libsystemd-dev zlib1g-dev make g++ tmux git jq wget libncursesw5 libtool autoconf -y
```

## Installing GHC and Cabal:
According to Cardano documentation, the fastest way to install GHC (Glassglow Haskell Compiler) and Cabal (Common Architecture for Building Applications and Libraries) is to use `ghcup`.

To install ghcup:
```console
curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | sh
```
Please follow the instructions and provide the necessary input to the installer.

`Do you want ghcup to automatically add the required PATH variable to "/home/ubuntu/.bashrc"?` - (P or enter)

`Do you want to install haskell-language-server (HLS)? `- (N or enter)

`Do you want to install stack?` - (N or enter)

`Press ENTER to proceed or ctrl-c to abort.` (enter)

Once complete, you should have ghc and cabal installed to your system.

Restart your shell/terminal after installing `ghcup`.

You can check if `ghcup` has been installed correctly by typing `ghcup --version` into the terminal. You should see something similar to the following:

```console
The GHCup Haskell installer, version v0.1.17.4
```
`ghcup` will install the latest stable version of `ghc`. However, as of the time of writing this, Input-Output recommends using `ghc 8.10.7`. So, we will use `ghcup` to install and switch to the required version.

```console
ghcup install ghc 8.10.7
ghcup set ghc 8.10.7
```

`ghcup` will install the latest stable version of cabal. However, as of the time of writing this, Input-Output recommends using `cabal 3.6.2.0`. So, we will use `ghcup` to install and switch to the required version.

```console
ghcup install cabal 3.6.2.0
ghcup set cabal 3.6.2.0
```

Check if we have the correct ghc and cabal versions installed with `ghc --version` and `cabal --version`:

```console
The Glorious Glasgow Haskell Compilation System, version 8.10.7

cabal-install version 3.6.2.0
compiled using version 3.6.2.0 of the Cabal library
```

## Download and Compile
Create a working directory to store the source-code and builds for the components.
```console
mkdir -p $HOME/cardano-src
cd $HOME/cardano-src
```

Next, download, compile and install libsodium.
```console
git clone https://github.com/input-output-hk/libsodium
cd libsodium
git checkout 66f017f1
./autogen.sh
./configure
make
sudo make install
```

Add the following environment variables to your shell profile. E.G $HOME/.zshrc or $HOME/.bashrc depending on what shell application you are using. Add the following to the bottom of your shell profile/config file so that the compiler can be aware that libsodium is installed on your system.

```console
export LD_LIBRARY_PATH="/usr/local/lib:$LD_LIBRARY_PATH"
export PKG_CONFIG_PATH="/usr/local/lib/pkgconfig:$PKG_CONFIG_PATH"
```

Once saved, we will then reload your shell profile to use the new variables. We can do that by typing source $HOME/.bashrc or source $HOME/.zshrc (depending on the shell application you use).

Now compile and install cardano-node and cardano-cli. But first, we have to make sure we are back at the root of our working directory:

```console
cd $HOME/cardano-src
```
Download the cardano-node repository:

```console
git clone https://github.com/input-output-hk/cardano-node.git
cd cardano-node
git fetch --all --recurse-submodules --tags
```

Switch the repository to the latest tagged commit:
```console
git checkout $(curl -s https://api.github.com/repos/input-output-hk/cardano-node/releases/latest | jq -r .tag_name)
```

## Configure the build options
I explicitly use the ghc version that we installed earlier. This avoids defaulting to a system version of ghc that might be newer or older than the one you have installed.
```console
cabal configure --with-compiler=ghc-8.10.7
```

If you are running non x86/x64 platform (eg. ARM) please install and configure LLVM with:
```console
sudo apt install llvm-9
sudo apt install clang-9 libnuma-dev
sudo ln -s /usr/bin/llvm-config-9 /usr/bin/llvm-config
sudo ln -s /usr/bin/opt-9 /usr/bin/opt
sudo ln -s /usr/bin/llc-9 /usr/bin/llc
sudo ln -s /usr/bin/clang-9 /usr/bin/clang
```

## Build and Install the Node
Now build the Haskell-based cardano-node to produce executable binaries.
```console
cabal build cardano-node cardano-cli
```

Install the newly built node and CLI commands to the $HOME/.local/bin directory:
```console
mkdir -p $HOME/.local/bin
cp -p "$(./scripts/bin-path.sh cardano-node)" $HOME/.local/bin/
cp -p "$(./scripts/bin-path.sh cardano-cli)" $HOME/.local/bin/
```

We have to add this line below our shell profile so that the shell/terminal can recognize that cardano-node and cardano-cli are global commands. ($HOME/.zshrc or $HOME/.bashrc depending on the shell application you use)

```console
export PATH="$HOME/.local/bin/:$PATH"
```

Once saved, reload your shell profile by typing source $HOME/.zshrc or source $HOME/.bashrc (depending on the shell application you use).

Check the version that has been installed:
```console
cardano-cli --version
cardano-node --version
```

🎉🎉🎉 Cardano node and cli are now installed on Linux 🎉🎉🎉




