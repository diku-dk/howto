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
