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
  manager](https://erda.dk/wsgi-bin/fileman.py).  But if you share a
  directory, this will also make files in all subdirectories available
  ([example](https://sid.erda.dk/cgi-sid/ls.py?share_id=FlhwY8rtfk)).

* To share files in a read-write manner with other ERDA users, you
  need to create a ["workgroup"](https://erda.dk/wsgi-bin/vgridman.py)
  and add them (using their KU username).

For additional help, consult the [user
guide](https://erda.ku.dk/public/ucph-erda-user-guide.pdf).

## Using ERDA with [`get-data.sh`](https://github.com/munksgaard/get-data.sh) to host large files in a git repository

Sometimes you want to host a project or some data on GitHub, but your project
contains files larger than GitHub allows (currently 100 MB). This is a guide for
how to set up `get-data.sh` and ERDA for such a scenario.

### Create an ERDA workgroup

The first thing you'll need to do is create an ERDA workgroup for your
project. For illustrative purposes, I'll call mine `get-data-test`. To create a
workgroup, you'll need to navigate to the [ERDA workgroups
page](https://erda.dk/wsgi-bin/vgridman.py), scroll all the way to the bottom,
enter the name of the workgroup we wish to create and click create.

### Create a share link for your workgroup

Now we need to create a share link for public access to the files in the
workgroup folder.

After creating the workgroup, find the newly created directory in [the file
manager on erda.dk](https://erda.dk/wsgi-bin/fileman.py), right-click it, choose
"Share Link" and "Create". In the prompt, specify which permissions should be
available for public access. Most likely, you should use "Read Access"
only. When the share link has been created, you'll get a popup noting the ID of
the share link. In my case it was `FXS2tnCbLE`.

### Upload your files

Using the web-interface, upload the files you want access to into your
workgroup. For instance, I've uploaded a file called "foo.blob" into my
`get-data-test` folder.

### Add the uploaded files to external-data.txt

Create a file named `external-data.txt` in the root of your repository. For each
file you've uploaded, add a line of the format `PATH URL SHA`. For instance:

```
external-data/foo.blob https://sid.erda.dk/share_redirect/FXS2tnCbLE/foo.blob 8c63faf15d9f8028826ed7d9a1647917a833092c1bd291f017b6618584124707
```

Now, calling `./get-data.sh external-data.txt` will download and verify the
files specified. You want to commit `external-data.txt` to your repository, but
not the files in `external-data`. Instead, add symlinks from the places
where you want to use the data to their location in `external-data`, and commit
the symlink:

```
ln -s -r external-data/foo.blob my/data/is/needed/here/foo.blob
git add my/data/is/needed/here/foo.blob
```
