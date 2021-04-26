# How do I VPN?

There are apparently two KU VPNs: a "new" one that is "more secure"
(it uses 2FA), and an older one.  This document describes how to
access the old ones.

Take a look at [the official
documentation](https://kunet.ku.dk/medarbejderguide/Sider/It/Fjernadgang-vpn.aspx)
(generally, the material on KUNet is better than its reputation).
However, you will notice that there is no documentation for Linux.

## Accessing the KU/DIKU VPN on Linux

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
and pick *Layer 2 tunneling protoco (L2TP)*.  Enter the following:

* **Gateway:** `vpn-diku-science.ku.dk`

* **Username:** your KU license plate (e.g. I put `mzd885`).

* **Password:** your KU password (e.g. I put `hunter2`).

* Under **IPsec Settings**:

  * Make sure *Enable IPsec tunnel to L2TP host* is checked

  * **Pre-shared key:** find it in the official documentation
    (e.g. the macOS guide).

  * Under *Advanced*, click the *Legacy Proposals* to enable the magic
    potpourri of *~algorithms~* that will satisfy the KU server.

* Under **PPP Settings**:

  * Under **Authentication**, make sure *only* the following methods
    are checked: *MSCHAP*, *MSCHAPv2*.

That should add the connection.  To *enable* it, do whatever you
normally do.  In particular, civilised tools like `nmtui` will work
fine (they cannot *add* VPNs but they can enable them just fine).
