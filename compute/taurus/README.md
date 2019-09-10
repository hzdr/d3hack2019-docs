# Taurus HPC cluster

Using taurus for machine learning is possible through two main avenues:

1. Using their [jupyterhub service](https://doc.zih.tu-dresden.de/hpc-wiki/bin/view/Compendium/JupyterHub) (you can spawn jupyterlab and standard jupyter notebooks)
2. Using the terminal and submitting [Batch Jobs](https://doc.zih.tu-dresden.de/hpc-wiki/bin/view/Compendium/Slurm) to Slurm

The official documentation for using `taurus` is available on [this wiki](https://doc.zih.tu-dresden.de/hpc-wiki/bin/view/Compendium/WebHome). If there are problems, that cannot be solved by people at the hackathon, please create a ticket with hpcsupport of `taurus` by sending a message to `hpcsupport@zih.tu-dresden.de`.

See the following pages for more detailed topics:
* [packages](sw.md)

## Getting an account

Accounts have already been attributed to each email address. When you receive your login, it will be secured with a generic password. The first thing you'll need to do, is change this password [here](https://selfservice.zih.tu-dresden.de/l/index.php/pswd).

Each team has its own project, like `p_gpuhack2` or `p_gpuhack18_1`. A project on taurus is needed to administer compute and storage budget. When submitting batch jobs, this information may be required during invocation of `sbatch` or `srun`. For a first time user of taurus, this is unlikely to be of any importance.

## Login with the terminal

All required details on how to login to taurus can be found on the [login wiki](https://doc.zih.tu-dresden.de/hpc-wiki/bin/view/Compendium/Login) of the cluster. The following 2 sections provide step-by-step examples assuming you are working with a UNIX like command line.

### On the TU Dresden campus 

From the hackathon venue (i.e. on the TU Dresden campus), `taurus` can be reached by connecting to the `eduroam` ESSID on the local wifi. When logged into the wifi, open a terminal and type:

``` bash
$ ssh gpu64@taurus.hrsk.tu-dresden.de
```
You will be prompted for your password. When successfull, you should see something like:

``` bash
gpu64@taurus.hrsk.tu-dresden.de's password: 
Last login: Fri Aug 30 16:27:46 2019 from login1.zih.tu-dresden.de
--------------------------------------------------------------------------------
nodes available:	1958/2043	nodes unavailable:	85/2043
gpus  available:	553/579		gpus  unavailable:	26/579
------------------------------------|-------------------------------------------
jobs running:   	4742	    |	cores in use:		37542
jobs pending:   	17321	    |	cores unavailable:	3180
jobs suspend:   	0	    |	gpus  in use:		272
jobs damaged:   	1	    |	
--------------------------------------------------------------------------------
#...
CPU-Quotas as of 2019-09-03 09:08
             Project    Used(h)   Quota(h)       % Comment
       p_gpuhack18_x          0      20000     0.0 

Disk-Quotas for /home as of 2019-09-03 09:50
                User   Used(GiB)  Quota(GiB)       % Comment
               gpu64         0.0        50.0     0.0 

Disk-Quotas for /projects as of 2019-09-03 09:54
             Project   Used(GiB)  Quota(GiB)       % Comment
       p_gpuhack18_x         0.0        50.0     0.0
#...
Run 'sinfo -T' for more details.
Dear Taurus users,

we have removed the partition "west", because it has run out of maintenance.
Please check your batch scripts.

Kind regards,
Danny Rotscher
```

Note, the output above documents a lot of telemetry that might be important to you. Most notably, your HPC project ID is mentioned in the table referring to `Disk-Quotas`. There you see your storage quota for `/home`, which is `50 GB` for user `gpu64` in this example. On top, the storage quota for your project is also listed under `Disk-Quotas for /projects`. In this example, it is `50 GB`.

### Outside the TU Dresden campus 

Outside the TU Dresden campus, direct access to taurus is not possible. You have to use `ssh` two times in order to enter taurus.

1. Make sure you are connected to the internet
2. Run  
``` shell
$ ssh gpu64@login.zih.tu-dresden.de`
```

3. You should see something like this:  
``` shell
Last login: Fri Aug 30 16:27:03 2019 from 95.91.3.248
####################################################################

Please mind that the Login Service has changed

Servername: login.zih.tu-dresden.de  

Alias:      login1.zih.tu-dresden.de 

login2 does no longer exist

####################################################################


gpu64@login:~>
```
4. Now issue another `ssh` command in order to enter taurus:  
``` shell
$ ssh gpu64@taurus.hrsk.tu-dresden.de
```
You will be prompted for your password. When successfull, you should see something like:

``` bash
gpu64@taurus.hrsk.tu-dresden.de's password: 
Last login: Fri Aug 30 16:27:46 2019 from login1.zih.tu-dresden.de
--------------------------------------------------------------------------------
nodes available:	1958/2043	nodes unavailable:	85/2043
gpus  available:	553/579		gpus  unavailable:	26/579
------------------------------------|-------------------------------------------
jobs running:   	4742	    |	cores in use:		37542
jobs pending:   	17321	    |	cores unavailable:	3180
jobs suspend:   	0	    |	gpus  in use:		272
jobs damaged:   	1	    |	
--------------------------------------------------------------------------------
#...
Run 'sinfo -T' for more details.
Dear Taurus users,

we have removed the partition "west", because it has run out of maintenance.
Please check your batch scripts.

Kind regards,
Danny Rotscher
```

#### SSH config for automatic proxy jump

In order to avoid having to manually issue two ssh commands each time or having
to pass extra parameters to perform a proxy jump, you can place the following in
your `~/.ssh/config`:

```
Host zih
 Hostname login2.zih.tu-dresden.de
 User <username>

Host taurus
 Hostname taurus.hrsk.tu-dresden.de
 User <username>
 ProxyJump zih

Host taurusexport
 Hostname taurusexport.hrsk.tu-dresden.de
 User <username>
 ProxyJump zih
```

Now you can, e.g.
```shell
$ ssh taurus
```
or
```
$ scp file taurusexport:/scratch/ws/gpu64-d3hack2019/
$ rsync -vau dir taurusexport:/scratch/ws/gpu64-d3hack2019/
```
See below for details about transferring data to taurus.

# Taurus Filesystem details

To explain, the coarse grain structure of the file system on taurus, let's assume you are user `gpu64` and you belong to project `p_gpuhack18_x`. Then, the following directories are of importance to you:

- `/home/gpu64` which is your home directory. This is an NFS mounted distributed file system. It is visible for read/write operations from all nodes across taurus.

- `/scratch/p_gpuhack18_x` This is your team's `/scratch` space. This is a **Lustre**-mounted high performance parallel file system. It is visible for read/write operations from all nodes across taurus. It is very fast and has a capacity of 5.1 PB.

- `/ssd/p_gpuhack18_x` This is your team's `/ssd` space. This is a **Lustre**-mounted high performance parallel file system based on SSD disks. It is visible for read/write operations from all nodes across taurus. It is ultrafast and has a capacity of 43 TB. Please use it only for smaller data (1-10GB), that you need extremely often.

The file system hierarchy on taurus is well explained on the [taurus wiki](https://doc.zih.tu-dresden.de/hpc-wiki/bin/view/Compendium/FileSystems). 

**IMPORTANT**: On Lustre based systems, it is not a good idea and create directories that have more than 1000-2000 files inside. If you happen to do that and each file is below 1MB, this will impact the performance of the entire file system and thus for all users of the cluster. Please do not create such directories.

## Workspaces

The operators of taurus make it mandatory that you use **workspaces** to manage your data. The details are documented on the [workspaces wiki](https://doc.zih.tu-dresden.de/hpc-wiki/bin/view/Compendium/WorkSpaces). The following will list some short examples on how to use these:

### For yourself

To create a workspace you have to inform the system, where you want the workspace (i.e. on which part of the filesystem) and for how long you intend to use it. In the following, I will create a workspace on `/scratch` (the large Lustre file system) for 60 days to come.

``` shell
$ ws_allocate -F scratch d3hack2019 60
Info: creating workspace.
/scratch/ws/gpu64-d3hack2019
remaining extensions  : 2
remaining time in days: 60
```

As the output reads, this workspace can now be found at `/scratch/ws/gpu64-d3hack2019`. Indeed, the workspace was created and is empty:

``` shell
$ ls -la /scratch/ws/gpu64-d3hack2019
total 24
drwx------   2 gpu64    p_gpuhack18_x  4096 Sep  3 11:23 ./
drwxr-xr-x 221 operator adm           20480 Sep  3 11:23 ../
```

At any time, you can remove this workspace again:

``` shell
$ ws_release -F scratch d3hack2019
$ ls /scratch/ws/gpu64-d3hack2019
/bin/ls: cannot access /scratch/ws/gpu64-d3hack2019: No such file or directory
```

As you can see, the workspace was removed and is not present anymore.

### For your team

Creating and removing a workspace for anyone in your HPC project, i.e. in your team, follows the same rules as documented above. If you want to create a workspace under `/scratch` for 60 days, do:

``` shell
$ ws_allocate -F scratch d3hack2019-Ateam 60
Info: creating workspace.
/scratch/ws/gpu64-d3hack2019-Ateam
remaining extensions  : 2
remaining time in days: 60
```

As the output reads, this workspace can now be found at `/scratch/ws/gpu64-d3hack2019-Ateam`. Indeed, the workspace was created and is empty:

``` shell
$ ls -la /scratch/ws/gpu64-d3hack2019-Ateam
total 24
drwx------   2 gpu64    p_gpuhack18_x  4096 Sep  3 11:23 ./
drwxr-xr-x 221 operator adm           20480 Sep  3 11:23 ../
```

At this point, your team mates will not be able to work with the data stored in this folder. You have to give them permissions to do so. Note, file permissions are a common source of errors. Feel free to dive into online docs like this [wikipedia page](https://en.wikipedia.org/wiki/File_system_permissions#Notation_of_traditional_Unix_permissions). However, if you spend more than 10 minutes trying to work out the right permissions, ask for help!

So let's allow your project/team mates to read and write data into the workspace we have just created. 

``` shell
$ chmod g+wrx /scratch/ws/gpu64-d3hack2019-Ateam
```

Your team mates can now `r`ead files that are stored there. They can `w`rite folders and files - this includes removing files/folders, renaming files and folders, ... They can also e`x`ecute files in this folder (this also includes `cd` into subfolders of `/scratch/ws/gpu64-d3hack2019-Ateam`). 

However, only the person that created this workspace (`gpu64` in this example) can release the workspace again.

``` shell
$ ws_release -F scratch d3hack2019-Ateam
$ ls /scratch/ws/gpu64-d3hack2019-Ateam
/bin/ls: cannot access /scratch/ws/gpu64-d3hack2019: No such file or directory
```

## Transferring Data onto Taurus

Taurus has two specialized data transfer nodes. Both nodes are accessible via `taurusexport.hrsk.tu-dresden.de`. Currently, only `rsync`, `scp` and `sftp` to these nodes will work. A login via SSH is not possible as these nodes are dedicated to data transfers. If you need advice on which software to use and how use it to transfer your data, see the [taurus wiki on moving data](https://doc.zih.tu-dresden.de/hpc-wiki/bin/view/Compendium/ExportNodes). In the following, we assume that we want to transfer the contents of an entire folder called `my_data` from your local machine (be it your laptop or your HPC cluster at your home institute). As the destination, the following assumes that you already created your workspace on taurus at `/scratch/ws/gpu64-d3hack2019` and we'll use that further on.

### Double-hop scp

If you are working from outside the TUD network, you have to transfer your data through a gateway server, called `login.zih.tu-dresden`. This matches what is documented in [Login with the terminal: Outside the TU Dresden campus](#Outside-TU-Dresden-campus).

We researched a command that allows you to tunnel through this machine in one go.

``` shell
$ scp -o ProxyCommand="ssh gpu64@login.zih.tu-dresden.de nc taurusexport.hrsk.tu-dresden.de 22" -r my_data gpu64@taurusexport.hrsk.tu-dresden.de:/scratch/ws/gpu64-d3hack2019
```

Feel free to copy and paste the above. Please don't forget to replace `gpu64` (with your login), `/scratch/ws/gpu64-d3hack2019` (with the destination directory you want to transfer data to) and `my_data` (with the name of the source directory you want to transfer) appropriately.

### VPN

You can also access the cluster through VPN. This is documented in general [here](https://tu-dresden.de/zih/dienste/service-katalog/arbeitsumgebung/zugang_datennetz/vpn?set_language=en). When doing so, you can transfer your files as in:

``` shell
$ scp -r my_data taurusexport.hrsk.tu-dresden.de:/scratch/ws/gpu64-d3hack2019
```


# Using Jupyterlab

Taurus offers a [jupyter hub service](https://taurus.hrsk.tu-dresden.de/jupyter) that allows you to spawn jupyterlab on a taurus node. This service is very convenient, but only available if your internet-connected device is on the TU Dresden campus. 

From the hackathon venue (i.e. on the TU Dresden campus), `taurus`'s jupyter service can be reached by connecting to the `eduroam` ESSID of the local wifi. When logged into the wifi, open a browser and open:

``` shell
https://taurus.hrsk.tu-dresden.de/jupyter
```

More documentation on how this service can be used is available [here](https://doc.zih.tu-dresden.de/hpc-wiki/bin/view/Compendium/JupyterHub).

### Global inputs, local outputs

As illuded to in [Workspaces](#workspaces), you have to manage your data in workspaces. Thus, we suggest that you put commonly used data inside a project wide workspace that all team members can access. Anything that you create personally, should land inside your personal workspace. 

For example, say your team's input data is stored as hdf5 files inside `/scratch/ws/gpu64-d3hack2019-Ateam` and the steps of [Workspaces: For your team](#for-your-team) have already been followed. You would like to apply a normalisation to your data and store it into a personal clone of the data. First, you create a workspace for you own:

``` shell
$ ws_allocate -F scratch d3hack2019 60
Info: creating workspace.
/scratch/ws/gpu64-d3hack2019
remaining extensions  : 2
remaining time in days: 60
```

Then, your python code would look like this (untested):

``` python
import h5py
import numpy

raw_matrix = None

#               vvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvvv Your team's workspace
with h5py.File("/scratch/ws/gpu64-d3hack2019-Ateam/raw.h5","r") as rawf:
    raw_matrix = rawf['matrix']
    raw_matrix = (raw_matrix - raw_matrix.mean())/raw_matrix.mean()
    
#               vvvvvvvvvvvvvvvvvvvvvvvvvvvvv Your own workspace
with h5py.File("/scratch/ws/gpu64-d3hack2019/normalized.h5","w") as outf:
    outf.create_dataset("normalized", data=raw_matrix)
    
```



# Using Fast.ai library in Jupyter notebook

First, ssh into Taurus and grab a ML machine (no need for a GPU for this right now), and activate anaconda.
``` bash
srun -p ml -n 1 --pty --mem-per-cpu=8000 bash
module load modenv/ml
module load PythonAnaconda
source activate /software/ml/JupyterHub/conda-powerai
```
Now, we need to make our own virtualenv that we can spin up as a ipython kernel in order to use this environment in jupyterlab:
``` bash
mkdir my-kernel
cd my-kernel/
virtualenv --system-site-packages fastai-kernel
source fastai-kernel/bin/activate
pip install ipykernel
python -m ipykernel install --user --name fastai-kernel --display-name="fastai-kernel"
```

We now install fastai into this environment. Since some dependencies of fastai did not properly install (e.g. spacy) we will have to install them by hand, like this:

``` bash
pip install --no-deps fastai
pip install dataclasses fastprocess numexpr
```

Now, open [jupyter](https://taurus.hrsk.tu-dresden.de/jupyter), select IBM Power, choose at least 1 GPU, choose recommended CPU's and press Spawn. Now, you should see in the "Launcher" tab not only a "python 3" kernel, but also a "fastai-kernel".



[[back to root page]](../../README.md)
