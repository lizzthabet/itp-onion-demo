# Share one thing! Onion-style
*ITP Camp 2022 demo*

<!-- TODO(lizz): Fill out this readme with instructions! -->

This repository includes a basic HTML site and Tor configuration that can be used to run a local web server that's accessible through onion routing. It follows the [demo guide](https://community.torproject.org/onion-services/setup/) ([onion link](http://xmrhfasfg5suueegrnc4gsgyi2tyclcy5oz7f5drnrodmdtob6t2ioyd.onion/onion-services/setup/index.html)) on the Tor Project to set up an onion service.

These instructions are WIP.

## Requirements
* A browser that can access the Tor network, like [Tor Browser](https://www.torproject.org/download/) (recommended) or [Brave Browser](https://brave.com/)
* The `tor` binary that will let you run an onion service locally. See [installation instructions](https://community.torproject.org/onion-services/setup/install/) ([onion link](http://xmrhfasfg5suueegrnc4gsgyi2tyclcy5oz7f5drnrodmdtob6t2ioyd.onion/onion-services/setup/install/index.html)) for each OS.
* A way to run a web server locally, like nginx, apache, or python
## Step 1. Run a local web server

### With python (beginner)

From this directory:
```sh
python3 -m http.server --directory ./public 3000
```

### With nginx (intermediate)
```sh
nginx -c absolute-path-to-this-directory/nginx.conf
```

## Step 2. Run Tor

From this directory:
```sh
tor -f torrc
```

To stop Tor, navigate to the terminal session that Tor is running in. You should see logs from the Tor process running. Press `CTRL + C` in the terminal window to stop Tor.

Alternatively, you can find the process id and send a termination signal to the process.

<!-- TODO(lizz): Add more information here -->

To reload:
```sh
kill -s hup <process-id>
```

Here's a quick excerpt from the tor manual page about what signals it handles:
```sh
Tor catches the following signals:

       SIGTERM
           Tor will catch this, clean up and sync to disk if necessary, and exit.

       SIGINT
           Tor clients behave as with SIGTERM; but Tor servers will do a controlled slow shutdown, closing listeners and waiting 30 seconds before exiting. (The delay can be
           configured with the ShutdownWaitLength config option.)

       SIGHUP
           The signal instructs Tor to reload its configuration (including closing and reopening logs), and kill and restart its helper processes if applicable.

       SIGUSR1
           Log statistics about current connections, past connections, and throughput.

       SIGUSR2
           Switch all logs to loglevel debug. You can go back to the old loglevels by sending a SIGHUP.

       SIGCHLD
           Tor receives this signal when one of its helper processes has exited, so it can clean up.

       SIGPIPE
           Tor catches this signal and ignores it.

       SIGXFSZ
           If this signal exists on your platform, Tor catches and ignores it.
```