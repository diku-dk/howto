# Using [ERDA](https://www.erda.dk/)

ERDA is a UCPH-managed data service for storing, sharing, analyzing
and archiving research data.  For DIKU researchers, it is probably the
easiest way to store large files for long periods of time.  You can
make data stored on ERDA publicly available, or share it with people
who know a password.  The ERDA website has pretty decent
documentation, but here are a few pointers (some of these links may
only work after you have logged in):

* [SFTP (i.e. `sshfs`)
  access](https://erda.dk/wsgi-bin/setup.py?topic=sftp) - even works
  with your public key, like an actual civilised system!

* Sharing files with the public requires using the [browser-based file
  manager](https://erda.dk/wsgi-bin/fileman.py).  But a trick is to
  share a directory, as this will also make files in all
  subdirectories available.

* To share files in a read-write manner with other ERDA users, you
  need to create a ["workgroup"](https://erda.dk/wsgi-bin/vgridman.py)
  and add them (using their KU username).
