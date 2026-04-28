# How do I VPN?

There is currently only one KU VPN, which uses 2FA.  This document describes how to
access it.

Take a look at [the official
documentation](https://kunet.ku.dk/medarbejderguide/Sider/It/Fjernadgang-vpn.aspx)
(generally, the material on KUNet is better than its reputation).
However, you will notice that there is no documentation for Linux.

## Accessing the KU VPN using NetworkManager

First you must set up the KU VPN.  You do this by going to
[vpn.ku.dk](https://vpn.ku.dk).  Note that you must use the app (NetIQ
Advanced Authenticator) - for some reason the VPN apparently does not
work with SMS-based 2FA or NemID/MitID.

Open the connection editor:

```
$ sudo nm-connection-editor
```

Then, press *Add connection*, pick *Cisco AnyConnect or openconnect*
and enter the following:

* **Gateway:** `vpn.ku.dk`

Leave everything else at its default settings.  Whenever you activate
the VPN, it will ask for your username, password (both of which can be
saved) and 2FA token (this must be entered every time you connect). If
*Cisco AnyConnect or openconnect* is not in the list of available
connection types, you would probably need to install some package(s),
depending on your setup (called `networkmanager-openconnect` on Arch
Linux).

## Accessing the KU VPN on Linux and macOS with OpenConnect

The new VPN uses the Cisco AnyConnect protocol. In order to access it, install
[OpenConnect](http://www.infradead.org/openconnect/) and run:

```sh
$ sudo openconnect vpn.ku.dk
```

You will be asked to enter your username and password.  You will also
need to enter a 2FA token from an authenticator app on your phone, or
via a discrete device (see the official documentation above for how to
set that up).

To install [OpenConnect](http://www.infradead.org/openconnect/) under 
macOS use `brew`:

```sh
$ brew install openconnect
```
Prerequisite: Homebrew (macOS only)
Installing openconnect via brew requires that Homebrew is installed
on your system.
Check if Homebrew is installed
Open a terminal and run:
```sh
$ brew --version
```
If Homebrew is installed, this will print the installed version number.
If you see command not found: brew, you need to install it.
Install Homebrew
Run the following command in your terminal:
```sh
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
After installation, you can verify it again with:
```sh
$ brew --version
```

## A handy script

Troels Henriksen from PLTC has written the following handy script, `kuvpn`,
which automates the use of KU's VPN. You should read the comments for how to use
it.

```sh
#!/bin/sh
#
# Script for connecting to the KU vpn with OpenConnect.
#
# Invoke it with a single option: the TOTP code from your MFA app. Example:
#
# $ kuvpn 320160
#
# You will need to modify the USER and PASSWORD_COMMAND definitions below unless
# you are Troels.
#
# Note that if 'sudo' asks for a password and you take too long to enter it, the
# TOPT code may have become invalid. Just try again.

# Modify the following to suit your preferences.
#
USER=mzd885 # Your username
PASSWORD_COMMAND="pass show kunet" # A command that retrives your password.

if [ $# -ne 1 ]; then
    echo "Usage: $0 TOTP"
    exit 1
fi

TOTP=$1 # First command line argument

# Pipe password and TOPT to openconnect over stdin.
#
# Explanation of options:
#   --useragent=AnyConnect   to talk properly to the server.
#
#   --background             makes openconnect run in the background after connecting - you
#                            can remove this if you want.
#
#   --syslog                 print logs to the system log, feel free to remove this.
#
#   --user=$USER             for telling it who you are.
#
#   --passwd-on-stdin        what it says.h
($PASSWORD_COMMAND; echo $1) | \
    sudo openconnect --useragent=AnyConnect --background --syslog --user=$USER --passwd-on-stdin vpn.ku.dk
```
