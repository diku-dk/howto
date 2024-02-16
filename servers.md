# Servers

There are servers at DIKU that are accessed through various esoteric
mechanisms.  You might need to talk to various people to get access,
but *once* you have access, this is how you connect and use them.

## The Hendrix cluster

Also known as *the IMAGE cluster*.  [See here for access and other
useful information.](https://diku-dk.github.io/wiki/slurm-cluster)

### Tips and tricks

* Remember that this is a shared system with many users.  Don't do
  clearly antisocial things.  For example, don't leave an interactive
  job idle for a long period of time on an in-demand node while you go
  for lunch.

* To run a command on an arbitrary node with an arbitrary GPU:

  ```
  srun --gres=gpu:1 --pty nvidia-smi
  ```

* To get a an interactive session on an arbitrary node:

  ```
  srun --pty bash
  ```

* To get an interactive session on a node with an arbitrary GPU:

  ```
  srun --gres=gpu:1 --pty bash
  ```

* To get an interactive session on a node with a specific GPU:

  ```
  srun -p gpu --gres=gpu:titanx:1 --pty bash
  ```

  See [this
  table](https://diku-dk.github.io/wiki/slurm-cluster#available-gpus)
  for available GPUs.  (Note that the table may not contain correct
  names - try lowercase versions of the indicated names.)

* To start an interactive session on a specific node:

  ```
  srun -p gpu -w hendrixgpu03fl --pty bash
  ```

  Note that you will also need to add an appropriate `--gres` to
  access the GPU(s) if you need them.

* To see all available resources and which nodes have them:

  ```
  sinfo -o "%60N  %5c  %10m  %20G "
  ```

* You can add `--exclusive` to `srun` to get an entire node to
  yourself, but you may be waiting a long time.  Otherwise you will
  only have exclusive access to the resources you asked for (e.g. you
  may get the GPU to yourself, but someone else may be hammering the
  CPU cores).

* You may want to add `--time=00:20:00` to `srun`, which will allow
  your command to run before jobs that demand infinite execution
  time...

#### Compiling Haskell

* Use `module load gmp` to make the GMP library available.  GHC
  usually expects this to be available.  Some SML compilers do, too.

* Use [ghcup](https://www.haskell.org/ghcup/) to install GHC and
  `cabal`.  You may need to use the following command to obtain a
  working GHC:

  ```
  $ ghcup install ghc --set -p x86_64-fedora27-linux
  ```

* Compilation with `cabal` is likely to make progress and then fail
  with errors such as:

  ```
  /home/.../dist-newstyle/tmp/package-registration--811774
  removeDirectoryRecursive:removeContentsRecursive:removeDirectory: unsatisfied
  constraints (Directory not empty)
  ```

  As far as I can determine, this is due to the network filesystem not
  following POSIX semantics.  However, this error is nondeterministic
  and `cabal` will make a bit of progress every time, so just keep
  retrying until it is done.

### Adding modules

The system has a standard [HPC module
system](https://hpc-wiki.info/hpc/Modules).  Use it with e.g. `module
load cuda`.  The set of available modules is maintained by KU IT, and
you can request more modules by opening a ticket at
https://serviceportal.ku.dk under *Forsknings-IT ->
Forskningsapplikationer og Service -> Rådgivning og support*.  They
seem pretty responsive.

## The Futhark servers

Dual-socket 2021-vintage Epyc machines with various GPUs.  Cosmin
Oancea and Troels Henriksen control access.  Hostnames:

* `futharkhpa01fl.unicph.domain`

* `futharkhpa02fl.unicph.domain`

* `futharkhpa03fl.unicph.domain`

You need to be [connected to the VPN](vpn.md) (or physically connected
to the *wired network* at DIKU) to connect to these machines (as
indicated by the hostnames above).

To get access to `/projects/futhark1`, you need to request access to
the COMP-PRJ-futhark1 group on
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
mkdir /projects/futhark1/data/$USER/dotcache && ln -sf /projects/futhark1/data/$USER/dotcache $HOME/.cache
mkdir /projects/futhark1/data/$USER/dotstack && ln -sf /projects/futhark1/data/$USER/dotstack $HOME/.stack
```

Make sure the directories do not already exist.

### Environment variables

To use the GPUs, set the following environment variables:

```
export CPATH=/opt/rocm/opencl/include:/usr/local/cuda/include:$CPATH
export LIBRARY_PATH=/opt/rocm/opencl/lib:/usr/local/cuda/lib64:$LIBRARY_PATH
export LD_LIBRARY_PATH=/opt/rocm/opencl/lib:/usr/local/cuda/lib64:$LD_LIBRARY_PATH
```

Consider adding this to your shell startup file.

### Granting access

On [identity.ku.dk](https://identity.ku.dk) add users to the group
`srv-futharkhpa-users`.

### Fiddling about with DKMS

[See this issue.](https://github.com/RadeonOpenCompute/ROCm/issues/1318)

### Compiling the Futhark compiler

Apparently, these new servers are simply _too powerful_ for `stack` and `cabal` to handle. Therefore, to reduce the time it takes to compile the futhark compiler, use `taskset` to limit the number of threads spawned. For example, this is how you would install using `stack`:

```
taskset -c 0-16 stack install
```

Doing so will cut your compilation time in half, if not more.
