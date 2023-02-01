# Updating your diku.dk/~foo homepage

You can update your department homepage by connecting with SFTP to
*kuid*@hjemmesider.diku.dk:

```
$ sftp kuid@hjemmesider.diku.dk
sftp> cd /var/www/hjemmesider.diku.dk/hjemmesider/ansatte/
sftp> put filename.html
```

You must be on the wired network or the [VPN](vpn.md).

Working through `sftp` is not very pleasant, but you can also use
`sshfs` to mount the remote file system locally:

```
$ mkdir -p hjemmesider
$ sshfs kuid@hjemmesider.diku.dk:/ hjemmesider/
```

Then you can edit your homepage using your normal text editor.
