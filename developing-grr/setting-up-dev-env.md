# Setting Up a Development Environment

> **NOTE:** This article describes setting up GRR development environment on Ubuntu 16 (Xenial) or Debian 9 (stretch). Most of the instructions below are applicable to other Linux distributions as well as to Mac OS X, although some details may differ from platform to platform.

## Step 1. Checkout GRR sources.

Navigate to the root GRR directory (or clone the repository if you have not done
it yet):

```bash
git clone https://github.com/google/grr
cd grr/
```

## Step 2. Set up Virtual Python environment.

Make sure that you have `virtualenv` installed. It should be available in
repositories of all popular Linux distributions. For example, to install it on
Ubuntu-based distros simply run:

```bash
sudo apt install virtualenv
```

To create a virtual environment you just execute the `virtualenv $DIR` command
where `$DIR` is the directory where you want it to be placed. The rest of the
manual will assume that the environment is created in `~/.virtualenv/GRR` like
this:

```bash
virtualenv ~/.virtualenv/GRR
```

After creating the environment you have to activate it:

```bash
source ~/.virtualenv/GRR/bin/activate
```

It is also advised to make sure that we are running a recent version of `pip` and have latest versions of a few related packages:

```bash
pip install --upgrade pip wheel setuptools six
```

For more information about creating and managing your virtual environments
refer to the [`virtualenv` documentation](https://virtualenv.pypa.io).

## Step 3. Set up Node.js environment.

Because GRR offers a user interface component it also needs some JavaScript code
to be built with Node.js. Fortunately, this can be done with `pip` inside our
virtual environment so that your system remains uncluttered.

To install Node.js simply do:

```bash
pip install nodeenv
nodeenv -p --prebuilt
```

Because the `nodeenv` command modifies our virtual environment, we also need to
reinitialize it with:

```bash
source ~/.virtualenv/GRR/bin/activate
```

## Step 4. Install GRR dependencies.

GRR depends on a few libraries that have to be installed on a system. Run this command to ensure you have them:

```bash
sudo apt-get install libssl-dev python-dev python-pip wget openjdk-8-jdk zip dh-systemd libmysqlclient-dev
```

## Step 5. Install Protocol Buffer compiler.

GRR relies on [protocol buffers](https://developers.google.com/protocol-buffers/) heavily. You need to have a protocol buffer compiler installed on your system in order to build it.

We have a script that checks out the right version of the Protobuf compiler into `${HOME}/protobuf`.

To run it (from the checked out GRR folder):

```bash
./travis/install_protobuf.sh linux
```

You also need to set PROTOC environment variable to point to the compiler (you can also add this to .bashrc):

```bash
export PROTOC=${HOME}/protobuf/bin/protoc
```

## Step 6. Install GRR packages.

GRR is split into multiple packages. For the development we recommend installing
all components. Assuming that you are in the root GRR directory run the
following commands:

> **NOTE**: order of the commands is important. Running these commands in wrong order will lead to PIP  fetching prebuilt GRR packages from PyPi. In such case, editing code in checked out GRR repository won't have any effect.

```bash
pip install -e ./grr/proto
pip install -e .
pip install -e ./grr/config/grr-response-server
pip install -e ./grr/client
pip install -e ./grr/config/grr-response-test
```

The `-e` (or `--editable`) flag passed to `pip` makes sure that the packages
are installed in a "development" mode and any changes you make in your working
directory are directly reflected in your virtual environment, no reinstalling
is required.

## Step 7. Install prebuilt GRR client templates.

If you want to repack GRR clients, you'll need a prebuilt GRR client templates package with templates for Linux/Mac/Windows. This should be installed using the following command:

```bash
pip install --no-cache-dir -f https://storage.googleapis.com/releases.grr-response.com/index.html grr-response-templates
```

## Step 8. Initialize GRR.

To initialize the development GRR setup, just run:

```bash
grr_config_updater initialize
```

Choose SQLite as the datastore and feel free to leave all other options at their default values.

## Step 9. Run GRR components.

You can use one of the commands below to run specific GRR server components.

Admin UI: `grr_admin_ui`

Frontend: `grr_frontend`

Worker: `grr_worker`

You can also run a local GRR client by executing `grr_client` command.

Adding `--verbose` flag to any of these commands would force GRR components to output debug information to stderr.

## Testing

Now you are ready to start the GRR development. To make sure that everything is
set-up correctly follow the [testing guide](running-tests.md).
