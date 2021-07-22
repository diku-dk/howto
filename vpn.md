# How do I VPN?

There are apparently two KU VPNs: a "new" one that is "more secure"
(it uses 2FA), and an older one.  This document describes how to
access both.

Take a look at [the official
documentation](https://kunet.ku.dk/medarbejderguide/Sider/It/Fjernadgang-vpn.aspx)
(generally, the material on KUNet is better than its reputation).
However, you will notice that there is no documentation for Linux.

## Accessing the *new* KU VPN using NetworkManager

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

## Accessing the *new* KU VPN on Linux and macOS with OpenConnect

The new VPN uses the Cisco AnyConnect protocol. In order to access it, install
[OpenConnect](http://www.infradead.org/openconnect/) and run:

```
$ sudo openconnect vpn.ku.dk
```

You will be asked to enter your username and password.  You will also
need to enter a 2FA token from an authenticator app on your phone, or
via a discrete device (see the official documentation above for how to
set that up).

You can write a script such that you don't have to enter your username
and password every time (do so and put it here!), but you will always
need to do the 2FA.

## Accessing the *old* DIKU VPN on Linux

The following assumes you use NetworkManager, which is the case on
most Linux systems nowadays.  You will need to add a VPN connection,
which you must do through the connection editor:

```
$ sudo nm-connection-editor
```

Depending on your desktop environment, you might also be able to
access the connection editor in some other way, and depending on your
user setup you might not need `sudo`.

Once there, press *Add connection* (the button looks like a `+` to me)
and pick *Layer 2 tunneling protocol (L2TP)*.  Enter the following:

* **Gateway:** `vpn-diku.science.ku.dk`

* **Username:** your KU license plate (e.g. I put `mzd885`).

* **Password:** your KU password (e.g. I put `hunter2`).

* Under **IPsec Settings**:

  * Make sure *Enable IPsec tunnel to L2TP host* is checked

  * **Pre-shared key:** find it in the official documentation
    (e.g. [the macOS guide](https://kunet.ku.dk/medarbejderguide/ITvejl/VPN%20-%20fjernadgang%20p%C3%A5%20SCIENCE%20PC%20Mac.pdf)).

  * Under *Advanced*, click the *Legacy Proposals* to enable the magic
    potpourri of *~algorithms~* that will satisfy the KU server.

* Under **PPP Settings**:

  * Under **Authentication**, make sure *only* the following methods
    are checked: *MSCHAP*, *MSCHAPv2*.

That should add the connection.  To *enable* it, do whatever you
normally do.  In particular, civilised tools like `nmtui` will work
fine (they cannot *add* VPNs but they can enable them just fine). Sometimes,
a reboot would be necessary after the setup is complete.

## VPN-over-SSH via the PLTC Raspberry Pi

An alternative (unofficial) VPN solution is to leverage an SSH
connection to the PLTC Raspberry Pi. (Alternatively, you can use it as
an HTTP or SOCKS proxy as well.)

With [`sshuttle`](https://github.com/sshuttle/sshuttle) connecting to
the university network amounts to a single command with all
authentication taken care of by SSH keys. Please see the
[pltc-pi](https://github.com/diku-dk/pltc-pi/) repo for details. If
you do not have access to the repo, contact
[@zfnmxt](https://github.com/zfnmxt).

### NixOS-specific (?)

You may need to create a `/etc/ipsec.secrets` file and a `/etc/ipsec.secrets.d`
directory yourself (it'll be populated by something else when you enable the
VPN, but apparently the file must exist in advance, and it's not clear who is
responsible for it):

```
sudo touch /etc/ipsec.secrets
sudo mkdir /etc/ipsec.secrets.d
```

### Arch Linux-specific (?)

You may also need to install `networkmanager-l2tp` and `strongswan` packages.
Furthermore, if *Legacy Proposals* is not available, you would have to manually provide the
list of legacy algorithms by setting:

* **Phase 1 Algorithms** to:

```
aes256-sha2_256-modp2048,aes256-sha2_256-modp1536,aes256-sha2_256-modp1024,aes256-sha1-modp2048,aes256-sha1-modp1536,aes256-sha1-modp1024,aes256-sha1-ecp384,aes128-sha1-modp1024,aes128-sha1-ecp256,3des-sha1-modp2048,3des-sha1-modp1024!
```
* **Phase 2 Algorithms** to:

```
aes256-sha1,aes128-sha1,3des-sha1!
```
