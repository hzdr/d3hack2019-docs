# Software packages

## On the ml partition

### Loading the PowerAI software stack

```
$ module load PowerAI/1.6.1
```

This offers somewhat recent versions of keras, tensorflow and pytorch. This should be suitable for most tasks. Installing dependencies with `pip install --user` is not recommended with this stack.

### preparing your own kernel to load in jupyter

The details for creating a custom "user kernel" in order to load *your* environment in a jupyter notebooks are detailed out [here](https://doc.zih.tu-dresden.de/hpc-wiki/bin/view/Compendium/JupyterHub). Here is a quick rundown:

First get a shell on a taurus `ml` node:

``` shell
$ srun --pty -p ml -n 1 -c 2 --mem-per-cpu 5772 -t 08:00:00 bash
```

Second, get a specific python version that comes with `virtualenv`:

``` shell
$ module load Python/3.6.6-fosscuda-2018b
```

Third, create a special folder inside your home directory:

``` shell
$ mkdir user-kernel
$ cd user-kernel
```

Create your own venv and use it:

``` shell
$ virtualenv --system-site-packages my-custom-kernel
$ source my-custom-kernel/bin/activate
```

Install ipython custom-kernel and give it a name:

``` shell
$ pip install ipykernel
$ python -m ipykernel install --user --name my-custom-kernel --display-name="my custom kernel"
```

At this point, you are free to `module load foo` where `foo` can be any package that you find:

``` shell
$ module load HDF5/1.10.5
$ pip install h5py
```

Finally, deactivate the environment.

``` shell
$ deactivate
```

**NOTE**: HPC support said that this recipe was not tested thorougly on taurus yet.


