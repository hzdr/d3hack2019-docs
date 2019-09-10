# Software packages

## On the ml partition

Important reads:

- https://towardsdatascience.com/a-guide-to-conda-environments-bc6180fc533


### Loading the PowerAI software stack

```
$ module load PowerAI/1.6.1
```

This offers somewhat recent versions of keras, tensorflow and pytorch. This should be suitable for most tasks. Installing dependencies with `pip install --user` is not recommended with this stack.


### Using Conda with the PowerAI stack

First get an interactive shell on a taurusml node. After that, load the modules required to install packages:

``` shell
$ module load modenv/ml PythonAnaconda
```

Use conda to manage your environments and to install packages into them:

```
$ conda create --name test-sklearn scikit-learn
```

After this is finished, check if it succeeded:

```
$ conda info --envs
```

To load it do

```
$ conda activate test-sklearn
```

To leave the conda environment, you should issue:

```
$ conda deactivate
```


### preparing your own kernel to load in jupyter

**WARNING** The following is untested!

The details for creating a custom "user kernel" in order to load *your* environment in a jupyter notebooks are detailed out [here](https://doc.zih.tu-dresden.de/hpc-wiki/bin/view/Compendium/JupyterHub). Here is a quick rundown:

First get a shell on a taurus `ml` node:

``` shell
$ srun --pty -p ml -n 1 -c 2 --mem-per-cpu 5772 -t 08:00:00 bash
```

Second, get a specific python version that comes with `virtualenv`:

``` shell
$ module load modenv/ml PythonAnaconda
$ source activate /software/ml/JupyterHub/conda-powerai 
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


