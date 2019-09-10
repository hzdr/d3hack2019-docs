# Software packages

Important reads:

- [a conda primer](https://towardsdatascience.com/a-guide-to-conda-environments-bc6180fc533)

## PowerAI on `taurusml`

### Loading the PowerAI software stack

```
$ module load modenv/ml PythonAnaconda
$ source activate /software/ml/JupyterHub/conda-powerai 
```

This offers somewhat recent versions of keras, tensorflow and pytorch. This should be suitable for most tasks. Installing dependencies with `pip install --user` is not recommended with this stack.


### Install missing packages from PowerAI stack

First get a fresh interactive shell on a `taurusml` node. After that, load the modules required to install packages:

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

### Your own kernel to load in jupyter

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
$ module load HDF5/1.10.5-gompic-2019a
$ pip install h5py
```

Finally, deactivate the environment.

``` shell
$ deactivate
```

**NOTE**: HPC support said that this recipe was not tested thorougly on taurus yet.


## Hints for Packages

### Using Fast.ai library in Jupyter notebook

First, ssh into Taurus and grab a ML machine (no need for a GPU for this right now), and activate anaconda.

``` bash
$ srun -p ml -n 1 --pty --mem-per-cpu=8000 bash
$ module load modenv/ml
$ module load PythonAnaconda
$ source activate /software/ml/JupyterHub/conda-powerai
```
Now, we need to make our own virtualenv that we can spin up as a ipython kernel in order to use this environment in jupyterlab:

``` bash
$ mkdir my-kernel
$ cd my-kernel/
$ virtualenv --system-site-packages fastai-kernel
$ source fastai-kernel/bin/activate
$ pip install ipykernel
$ python -m ipykernel install --user --name fastai-kernel --display-name="fastai-kernel"
```

We now install fastai into this environment. Since some dependencies of fastai did not properly install (e.g. spacy) we will have to install them by hand, like this:

``` bash
$ pip install --no-deps fastai
$ pip install dataclasses fastprocess numexpr
```

Now, open [jupyter](https://taurus.hrsk.tu-dresden.de/jupyter), select IBM Power, choose at least 1 GPU, choose recommended CPU's and press Spawn. Now, you should see in the "Launcher" tab not only a "python 3" kernel, but also a "fastai-kernel".


## x86_64



On taurus, there are 2 other partitions that might be of interest for teams, `gpu1` (onld K20m GPUs) and `hpdlf` (16 intel nodes with 3 GTX 1080 cards each).

The details for creating a custom "user kernel" in order to load *your* environment in a jupyter notebooks are detailed out [here](https://doc.zih.tu-dresden.de/hpc-wiki/bin/view/Compendium/JupyterHub). Here is a quick rundown:

First get a shell on a taurus `ml` node:

``` shell
$ srun --pty -p ml -n 1 -c 2 --mem-per-cpu 5772 -t 08:00:00 bash
```

Second, get a specific python version that comes with `virtualenv`:

``` shell
$ module load modenv/ml PythonAnaconda
```

Third, create a special folder inside your home directory:

``` shell
$ mkdir user-kernel
$ cd user-kernel
```

Create your own venv and use it:

``` shell
$ python3 -m venv --system-site-packages my-custom-x86
$ source my-custom-x86/bin/activate
```

Then install the ipython kernel

``` shell
$ pip install ipykernel
$ python -m ipykernel install --user --name my-custom-x86 --display-name="my custom x86 kernel"
```

Now you can install all that you want:

```
$ pip3 install torch torch.vision fastai
$ module load HDF5/1.10.2-fosscuda-2018b
$ pip install h5py
```

Leave the environment now.

```
$ deactivate
```

This has been tested to work.

