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

### multi-gpu multi-node training

For those interested in parallelizing training, we found a way to use `horovod` on the power architecture. 

To do so, use the following script with sbatch:

```
$ cat hvd.sh
#!/bin/bash -l
#SBATCH -p ml
#SBATCH -t 00:05:00
#SBATCH --ntasks=8
#SBATCH --ntasks-per-node=1
#SBATCH --gres=gpu:1
#SBATCH --mem=0
#SBATCH -o hvd_hw2_distributed_%J.out
#SBATCH -e hvd_hw2_distributed_%J.err

module load modenv/ml
module load OpenMPI/3.1.4-gcccuda-2018b
module load PythonAnaconda/3.6 
module load cuDNN/7.1.4.18-fosscuda-2018b
module load CMake/3.11.4-GCCcore-7.3.0
module load NCCL/2.3.7-fosscuda-2018b

source /lustre/ssd/ws/gpu46-hackathon-software/.venv/hackathon-kernel/bin/activate

#Note: `python` here is a python3
srun python hvd.py 
```

The python script mentioned in the last line of `hvd.sh` looks like this:

```
$ cat hvd.py
import horovod.torch as hvd
hvd.init()

print("Hello from:",hvd.rank())
```

In order to submit this to taurus, do 

```
$ sbatch hvd.sh
```

In the configuration above, this will request 8 GPUs on 8 different nodes. The instructions on how to compile horovod are contained in this [script](install_hvd_taurus_ml.md).

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

First get a shell on a taurus `hpdlf` node:

``` shell
$ srun --pty -p hpdlf -n 1 -c 2 --gres=gpu:1 --mem-per-cpu 5772 -t 08:00:00 bash
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

