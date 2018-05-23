MERCANTE Explorer
===================

MERCANTE Explorer is a free block chain browser for [MERCANTE](http://www.MERCANTE.com/) blockchains.

https://github.com/MERCANTE/MERCANTE-explorer

    Copyright(C) 2015,2016,2017 by Coin Sciences Ltd.
    Copyright(C) 2011,2012,2013 by Abe developers.
    License: GNU Affero General Public License, see the file LICENSE.txt.
    Portions Copyright (c) 2010 Gavin Andresen, see bct-LICENSE.txt.


Welcome to MERCANTE Explorer!
===============================

This software reads the MERCANTE block file, transforms and loads the
data into a database, and presents a web interface similar to that
popularized by Bitcoin block explorers like http://blockexplorer.com/.

MERCANTE Explorer is a fork of the popular [Abe](https://github.com/bitcoin-abe/bitcoin-abe) project to add support for MERCANTE blockchains with assets and permissions.  MERCANTE nodes must be online for all explorer functions to work.

MERCANTE Explorer is still under development, so things may break or change!


System Requirements
-------------------

You must have Python 2.x installed on your system to run the explorer.

On Ubuntu, you will need to install the following dependencies:

    sudo apt-get install sqlite3 libsqlite3-dev
    sudo apt-get install python-dev
    sudo apt-get install python-pip
    sudo pip install --upgrade pip
    sudo pip install pycrypto

On CentOS, you will need to install the following dependencies:

    sudo yum install epel-release
    sudo yum install python-pip
    sudo pip install --upgrade pip
    sudo yum install sqlite-devel
    sudo yum install python-devel
    sudo yum groupinstall "Development tools"
    sudo pip install pycrypto


MERCANTE Compatibility
------------------------

The explorer currently supports MERCANTE 1.0.x versions (since 1.0 alpha 29) and protocol versions up to `10009`, which is the default for MERCANTE 1.0.x. You can check the protocol version of an existing chain using the `getinfo` command.

**Note that this Explorer does not yet support MERCANTE 2.0 preview releases.**

Installation
------------

To install MERCANTE Explorer for the current user (recommended):

    cd MERCANTE-explorer
    python setup.py install --user

If you have root permission and want to install MERCANTE Explorer for all users on the system:

    cd MERCANTE-explorer
    sudo python setup.py install

The explorer needs to connect to a local MERCANTE node.  Before configuring the explorer, let's make sure you have a MERCANTE blockchain up and running.


Create and launch a MERCANTE Blockchain
-----------------------------------------

If you do not yet have a chain you want to explore, [Download MERCANTE](http://www.MERCANTE.com/download-install/) to install MERCANTE and create a chain named ````chain1```` as follows:

    MERCANTE-util create chain1
    MERCANTEd chain1 -daemon

By default the [runtime parameter](http://www.MERCANTE.com/developers/runtime-parameters/) ````txindex```` is enabled so that the node keeps track of all transactions across the blockchain, and not just those for the node's wallet. This is required for the explorer to work correctly.

The explorer supports viewing streams which the node has subscribed to.  Launch MERCANTE with the runtime parameter ````autosubscribe=streams```` to automatically subscribe to every stream.

_The rest of this document assumes your blockchain is named ````chain1````. If not, please substitute accordingly._


Configure MERCANTE.conf
-------------------------

The explorer needs to communicate with the blockchain using JSON-RPC.  When you created the blockchain, the JSON-RPC connection details were automatically created by MERCANTE and stored in a file named ````MERCANTE.conf````.

The explorer will read this file. If you examine the file you will see a username and password have been auto-generated.

    cd ~/.MERCANTE/chain1/
    cat MERCANTE.conf

All you need to do is add the RPC port number. Copy the ````default-rpc-port```` value from ````params.dat```` and add an entry to ````MERCANTE.conf```` as follows:

    cd ~/.MERCANTE/chain1/
    grep rpc params.dat
    # Make a note of the default-rpc-port value, let's say it's 1234, and add it to MERCANTE.conf
    echo "rpcport=1234" >> MERCANTE.conf


Configure the Explorer
----------------------

The bundled example config file ````chain1.example.conf```` can be used as a template for your own chain.

    cd MERCANTE-explorer
    cp chain1.example.conf chain1.conf

You can store the config file ````chain1.conf```` anywhere you want. When you launch the explorer you can specify the location of your config file. By default, it will look for a config file in the current directory.

The following changes can be made:

* Change ````port```` to the port number for serving web pages (make sure your host's firewall allows traffic through that port).
* Change ````host```` to ````0.0.0.0```` to serve web pages to anybody (make sure there is only a single host entry in the config file).
* Change ````dirname```` to match the directory for your blockchain.
* Change ````chain```` to set how the chain should be listed in the explorer.
* Change ````connect-args```` for the location to store the explorer database.

Note: The explorer will automatically read MERCANTE specific parameters such as the magic handshake, address checksum, version and script version bytes from ````params.dat````.


Launch the Explorer
-------------------

To load existing blockchain data into the explorer:

    cd MERCANTE-explorer
    python -m Mce.abe --config chain1.conf --commit-bytes 100000 --no-serve

Look for output such as:

    block_tx 1 1
    block_tx 2 2
    ...

This step may take several minutes to even days depending on chain size and hardware.

To launch the explorer and serve web pages from your local computer:

    cd MERCANTE-explorer
    python -m Mce.abe --config chain1.conf

By default, the explorer will be listening for web requests on port 2750, unless you changed it in the Explorer's configuration file.  In your browser visit:

    http://localhost:2750/

To launch the explorer on a server, make sure the explorer is not accidentally terminated when you close your SSH terminal connection.

    cd MERCANTE-explorer
    nohup python -m Mce.abe --config chain1.conf &

To check the explorer is runnning, in your browser visit:

    http://ip_address_of_server:2750/


Reset the Explorer
------------------

To start over with new chain data for a chain of the same name, simply:

1. Stop the explorer.

2. Delete the explorer database file ````chain1.explorer.sqlite```` (set in ````connect-args```` parameter in ````chain1.conf````).

3. Launch the explorer as above.


Advanced Configuration Options
------------------------------

The following options can be set in the explorer config file ````chain1.conf```` or as a command-line option with the format ````--option=value````.

* ````home_refresh_interval_secs```` specifies how frequently the home page refreshes itself when open in a browser window.  The default is every 60 seconds.
* ````recent_tx_interval_ms```` specifies how frequently the home page refreshes the recent transactions table including any mempool transactions.  The default is every 5000 milliseconds (5 seconds),
* ````catch_up_tx_interval_secs```` specifies how frequently the explorer will load blockchain transactions into the explorer database.  The default is every 60 seconds.


Misc Notes
----------
* Currently it is not recommended to configure multiple chains in one config file as the search function does not search across chains for an address

* You can run two instances of the Explorer with the same config file, with one being passed the ````--no-serve```` argument and the other ````--no-load````, so that one instance only loads data into the database, and the other only serves web pages.
