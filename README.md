# 🧅 Share one thing! Onion-style 🧅
*ITP Camp 2022 demo*

<!-- TODO(lizz): Fill out this readme with instructions! -->

This repository includes a basic HTML site and Tor configuration to be used to run a local web server that's accessible through onion routing. It follows the [Set Up Your Onion Service guide](https://community.torproject.org/onion-services/setup/) ([onion link](http://xmrhfasfg5suueegrnc4gsgyi2tyclcy5oz7f5drnrodmdtob6t2ioyd.onion/onion-services/setup/index.html)) on the Tor Project.

These instructions are WIP.

## Requirements
* The `tor` binary that will let you run an onion service locally. See [installation instructions](https://community.torproject.org/onion-services/setup/install/) ([onion link](http://xmrhfasfg5suueegrnc4gsgyi2tyclcy5oz7f5drnrodmdtob6t2ioyd.onion/onion-services/setup/install/index.html)) for each OS.
  * Note: little-t-tor (the `tor` binary) is most easily installed with Homebrew on macOS. The installation instructions vary based on your Linux package manager, so follow those instructions closely. For Windows users, I believe `tor` can be compiled and installed from the [source code](https://www.torproject.org/download/tor/index.html) ([onion link](http://2gzyxa5ihm7nsggfxnu52rck2vv4rvmdlkiu3zzui5du4xyclen53wid.onion/download/tor/index.html)), but I haven't tried this myself. 
* A browser that can access the Tor network, like [Tor Browser](https://www.torproject.org/download/) (recommended) or [Brave Browser](https://brave.com/)
  * Note: The Tor Project 
* A way to run a web server locally, like nginx, apache, or python

> Tip: Search for the `TODO` comments in this directory to find all the changes you'll need to make to run the demo.

## Step 1: Personalize your website

All the files that are under the `/public` directory will be available on your website. The default page is `index.html` and it includes a place for you to introduce yourself and display an image of your work.

**Instructions**: 
1. Update the `index.html` page with your name, pronouns, and any text you'd like to include.
2. Add an image of something you'd like to share to the `public/` folder. You can name it `my-art` and replace the existing image. *Note*: if your image isn't a `png`, you'll need to update the image filename in the `<img>` tag.

## Step 2: Run a local web server

The next step is running a local static web server that will serve all the files in the `/public` directory. Below are examples using `python3` and `nginx` using the sample configuration file in this repo.

You can also use another web server of your choosing, as long as it's serving content from `/public` and listening on port 3000.

Both code examples assume that you're running the command from the repository's root directory.

**With python (recommended for beginners)**

```sh
python3 -m http.server --directory ./public 3000
```

**With nginx (intermediate, used in the Tor Project's documentation)**:

```sh
nginx -c "$(pwd)/nginx.conf"
```

*Note*: The path to the `nginx.conf` should be absolute, not relative to the directory that you're running the command from.

After starting your web server, verify that you can access the site by visiting `localhost:3000` in your browser.

## Step 3. Start your `hidden_service` site

From this directory:
```sh
tor -f torrc
```

You should see ...

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