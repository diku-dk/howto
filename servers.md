# Servers

There are servers at DIKU that are accessed through various esoteric
mechanisms.  You might need to talk to various people to get access,
but *once* you have access, this is how you connect.

## The old APL servers

2014-vintage dual-socket Xeons with various GPUs.  Cosmin Oancea
controls access.  They are accessed by first SSHing to the gateway
server `ssh-diku-apl.science.ku.dk` using your KU username and
password, and then SSHing on from there using one of the following hostnames:

* `gpu01-diku-apl`

* `gpu02-diku-apl`

* `gpu03-diku-apl`

* `gpu04-diku-apl`

* `titan-diku-apl`

* `phi-diku-apl`


### Compiling the Futhark compiler on the old APL servers

**These instructions are only for when you need to compile Haskell
programs (such as the Futhark compiler itself) on the servers, not if
you just want to compile Futhark programs.**

The network file system used by SCIENCE-IT on the GPU servers has a
bug which makes the `open()` syscall not set the executable bit
properly on new files for some users.  It can be set subsequently with
the `chmod()` syscall, but the `ld.gold` linker that GHC uses by
default quite reasonably expects that basic system calls work.  The
end result is that `ld.gold` produces non-executable binaries, and
this makes GHC malfunction.  The suggested workaround is to
transparently replace `ld.gold` with the older linker `ld`, which
happens to implement file creation by separately calling `chmod()`.
You do this by ensuring `$HOME/.local/bin` is first on your `PATH`,
and then symlinking:

```
$ ln -s `which ld` $HOME/.local/bin/ld.gold
```

This will make GHC (and all other tools) use `ld` instead of `ld.gold`.

Furthermore, for SCIENCE-IT reasons, you don't actually own your home directory
on those machines, and this tends to confuse some software. Therefore, you
should manually create a stack root before proceeding with compilation
of the Futhark compiler:

```
$ mkdir $HOME/.stack
```

* **Yes**, it is absolutely ridiculous that basic filesystem
  operations don't work in TYOOL 2020.  Blame whichever buggy NFS
  servers is actually hosting these home directories.

* **No**, it is unlikely that this will ever be fixed.  We reported
  this to SCIENCE-IT a year ago, and the issue can be demonstrated by
  a tiny C program, but have not yet heard anything back.


## The Futhark servers

Dual-socket 2021-vintage Epyc machines with various GPUs.  Cosmin
Oancea and Troels Henriksen control access.  Hostnames:

* `futharkhpa01fl.unicph.domain`

* `futharkhpa02fl.unicph.domain`

* `futharkhpa03fl.unicph.domain`

You need to be [connected to the VPN](vpn.md) (or physically connected
to the *wired network* at DIKU) to connect to these machines (as
indicated by the hostnames above).

The home directories are subject to a 10GiB quota. Instead, you should use subdirectories in `/projects/futhark1/[YOUR-USERNAME]` for everything.

To get access to `/projects/futhark1`, you need to request access to
the SEC-HPC-PRJ-futhark1 group on
[identity.ku.dk](https://identity.ku.dk).

### Working around the low disk quotas

The (networked) home directories have a 10GiB space limit.  Instead,
store important data in the (also networked) directories in
`/projects` (assuming you have access to some of them).  The
differerence between the `apps`, `data`, and `scratch` folders is
immaterial; pick whatever you think sound best and make a folder for
yourself.

Many build tools like to put build artifacts in hidden directories in
your home directory.  These will quickly run afoul of the quota.  The
solution is add symlinks pointing to places in `/projects`, e.g.:

```
mkdir /projects/futhark1/data/$USER/dotcabal && ln -sf /projects/futhark1/data/$USER/dotcabal $HOME/.cabal
mkdir /projects/futhark1/data/$USER/dotlocal && ln -sf /projects/futhark1/data/$USER/dotlocal $HOME/.local
mkdir /projects/futhark1/data/$USER/dotstack && ln -sf /projects/futhark1/data/$USER/dotstack $HOME/.stack
```

Make sure the directories do not already exist.

### Granting access

On [identity.ku.dk](https://identity.ku.dk) add users to the group
`srv-futharkhpa-users`.

### Fiddling about with DKMS

[See this issue.](https://github.com/RadeonOpenCompute/ROCm/issues/1318)
