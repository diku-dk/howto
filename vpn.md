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

You can write a script such that you don't have to enter your username
and password every time (do so and put it here!), but you will always
need to do the 2FA.

To install [OpenConnect](http://www.infradead.org/openconnect/) under 
macOS use `brew`:

```sh
$ brew install openconnect
```
