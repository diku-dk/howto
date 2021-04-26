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

## The Futhark servers

Dual-socket 2021-vintage Epyc machines with various GPUs.  Cosmin
Oancea and Troels Henriksen control access.  Hostnames:

* `futharkhpa01fl.unicph.domain`

* `futharkhpa02fl.unicph.domain`

* `futharkhpa03fl.unicph.domain`

You need to be [connected to the VPN](vpn.md) (or physically connected
to the *wired network* at DIKU) to connect to these machines (as
indicated by the hostnames above).

### Granting access

On [identity.ku.dk](https://identity.ku.dk) add users to the group
`srv-futharkhpa-users`.
