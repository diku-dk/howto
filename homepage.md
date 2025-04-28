# Updating your diku.dk/~foo homepage

Your department homepage is stored on the "N drive", specifically
`https://ucph-ku.zoom.us/j/4582423981`. To gain access, you must write to
[Morten
Engell-Nørregaard](https://di.ku.dk/english/staff/administration/?id=246448&vis=medarbejder).
Note that you will get write access to *all* homepages, so please be careful.

Once you have been granted access, you access your homepage as you access
anything else on the "N drive". In practice, I have found that you do this by
logging into some KU system (e.g. [the DIKU cluster](servers.md)) and looking in
`~/ucph/ndir/WEB-hjemmesider.diku.dk/`.

For greater convenience, you can also use `sshfs` to mount the remote file
system locally. However, in that case the `ucph` symlink in your home directory,
will not work, as it is absolute and not relative. You can instead directly
access `/maps/groupdir/$USER/WEB-hjemmesider.diku.dk`, where you should replace
`$USER` with your username.
