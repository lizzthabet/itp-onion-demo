# 🧅 Share one thing! Onion-style 🧅
*ITP Camp 2022 demo*

This repository includes a basic HTML site and Tor configuration to be used to run a local web server that's accessible through onion routing. It follows the [Set Up Your Onion Service guide](https://community.torproject.org/onion-services/setup/) ([onion link](http://xmrhfasfg5suueegrnc4gsgyi2tyclcy5oz7f5drnrodmdtob6t2ioyd.onion/onion-services/setup/index.html)) on the Tor Project.

These instructions are WIP, but the demo content is finalized!

## Requirements
* The `tor` binary that will let you run an onion service locally. See [installation instructions](https://community.torproject.org/onion-services/setup/install/) ([onion link](http://xmrhfasfg5suueegrnc4gsgyi2tyclcy5oz7f5drnrodmdtob6t2ioyd.onion/onion-services/setup/install/index.html)) for each OS.
  * Note: little-t-tor (the `tor` binary) is most easily installed with Homebrew on macOS. The installation instructions vary based on your Linux package manager, so follow those instructions closely. For Windows users, I believe `tor` can be compiled and installed from the [source code](https://www.torproject.org/download/tor/index.html) ([onion link](http://2gzyxa5ihm7nsggfxnu52rck2vv4rvmdlkiu3zzui5du4xyclen53wid.onion/download/tor/index.html)), but I haven't tried this myself. 
* A browser that can access the Tor network, like [Tor Browser](https://www.torproject.org/download/) (recommended) or [Brave Browser](https://brave.com/)
  * Note: The Tor Project only endorses Tor Browser for visiting websites with Tor, since it can't guarantee external implementations. Plus, Tor Browser has other privacy features, like steps it takes to prevent fingerprinting.
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

Edit the `nginx.conf` file according to the `TODO` comment in that file. Then, start the nginx server.

```sh
nginx -c "$(pwd)/nginx.conf"
```

*Note*: The path to the `nginx.conf` should be absolute, not relative to the directory that you're running the command from.

After starting your web server, verify that you can access the site by visiting `localhost:3000` in your browser. (Tor Browser won't connect to `localhost`, so use your default browser instead.) **It's important to verify that your local server is up and your website is accessible, otherwise the following steps won't work.**

## Step 3. Start your `hidden_service` site

Next, we'll config and start a "location-hidden service" that will connect to your local web server and make it accessible over the Tor network.

A Tor service is configured through a `torrc` file, which is included in this repo. The default `torrc` file contains a lot of information about various settings with helpful comments about them. You're welcome to read through the config to learn more about how to set up various parts of the Tor ecosystem, but here we'll focus on the two settings you'll need to run your onion site, (one of which you'll need to update).

**Instructions**:
1. In the `torrc` file, go to line 77 with the `TODO` comment.
2. Replace the directory listed next to `HiddenServiceDir` with the directory of this repository. Then add `/hidden_service/` to the end of that path. *Note*: Do not create the `/hidden_service/` directory yourself. Tor will create the directory with the correct permissions when it runs for the first time.
3. In your terminal from this directory, run:
    ```sh
    tor -f torrc
    ```
4. Tor is now running! In this directory, you should see a new `hidden_service` folder that's been created. You can open up the `hidden_service/hostname` file to view your onion site address, or you can run:
   ```sh
   pbcopy < ./hidden_service/hostname
   ```
   to copy the onion address to your clipboard.
5. Open Tor Browser and navigate to your onion site's address. You should see the same website as what's being served on your `localhost:3000`.

Congratulations, you have an onion site!

## Debugging

<!-- TODO (lizz): Add information about nginx testing, tor config verification, and local server access. Add info on tor restart/reload. -->
### General questions

For general questions about running `tor` and its configuration, you can look at the `man` page, which contains helpful information about Tor options.
```sh
man tor
```
You can search the `man` page by pressing `/` and typing the term you're searching for. `n` will navigate forward in the search results, `p` will navigate backwards, and `enter` will exit the search. `q` will exit the `man` page.

### The onion address isn't loading

First, verify that your website is accessible locally at `localhost:3000`. If it's not, then it won't be available over Tor, since Tor is routing network requests to your local server.

Second, check the log output of the `tor -f torrc` process you ran. If you see an error message in the logs, follow their instructions and investigate the specific error.

You can get helpful feedback about the validity of your configuration file by running:
```sh
tor -f torrc --verify-config
```

If both the web server and `tor` are running properly, double-check that you copied the onion address from the `hidden_service/hostname` file correctly.

If none of those solve the problem, it's possible that the onion site is taking a second to become available over Tor. Try accessing your site again in a minute.

## Teardown

<!-- TODO (lizz): Polish this section a bit -->

To stop Tor, navigate to the terminal session that Tor is running in. You should see logs from the Tor process running. Press `CTRL + C` in the terminal window to stop Tor.

Alternatively, you can find the process id and send a termination signal to the process.

<!-- TODO(lizz): Add more information here -->

To reload Tor:
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