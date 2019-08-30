# Taurus HPC cluster

Using taurus for machine learning is possible through two main avenues:

1. Using their [jupyterhub service](https://doc.zih.tu-dresden.de/hpc-wiki/bin/view/Compendium/JupyterHub) (you can spawn jupyterlab and standard jupyter notebooks)
2. Using the terminal and submitting [Batch Jobs](https://doc.zih.tu-dresden.de/hpc-wiki/bin/view/Compendium/Slurm) to Slurm

The official documentation for using `taurus` is available on [this wiki](https://doc.zih.tu-dresden.de/hpc-wiki/bin/view/Compendium/WebHome). If there are problems, that cannot be solved by people at the hackathon, please create a ticket with hpcsupport of `taurus` by sending a message to `hpcsupport@zih.tu-dresden.de`.

## Getting an account

Accounts have already been attributed to each email address. When you receive your login, it will be secured with a generic password. The first thing you'll need to do, is change this password [here](https://selfservice.zih.tu-dresden.de/l/index.php/pswd).

Each team has its own project, like `p_gpuhack2` or `p_gpuhack18_1`. A project on taurus is needed to administer compute and storage budget. When submitting batch jobs, this information may be required during invocation of `sbatch` or `srun`. For a first time user of taurus, this is unlikely to be of any importance.

## Login with the terminal

### On TU Dresden campus 

From the hackathon venue (i.e. on the TU Dresden campus), `taurus` can be reached by connecting to the `eduroam` ESSID on the local wifi. When logged into the wifi, open a terminal and type:

``` bash
$ ssh gpu44@taurus.hrsk.tu-dresden.de
```
You will be prompted for your password. When successfull, you should see something like:

``` bash
gpu44@taurus.hrsk.tu-dresden.de's password: 
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

### Outside TU Dresden campus 

Outside the TU Dresden campus, direct access to taurus is not possible. You have to use `ssh` two times in order to enter taurus.

1. Make sure you are connected to the internet
2. Run  
``` shell
$ ssh gpu44@login.zih.tu-dresden.de`
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


gpu44@login:~>
```
4. Now issue another `ssh` command in order to enter taurus:  
``` shell
$ ssh gpu44@taurus.hrsk.tu-dresden.de
```
You will be prompted for your password. When successfull, you should see something like:

``` bash
gpu44@taurus.hrsk.tu-dresden.de's password: 
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

# Taurus Filesystem details

To explain, the coarse grain structure of the file system on taurus, let's assume you are user `gpu44` and you belong to project `p_gpuhack18_2`. Then, the following directories are of importance to you:

- `/home/gpu44` which is your home directory. This is an NFS mounted distributed file system. It is visible for read/write operations from all nodes across taurus.

- `/scratch/p_gpuhack18_2` This is your team's `/scratch` space. This is a **Lustre**-mounted high performance parallel file system. It is visible for read/write operations from all nodes across taurus. It is very fast and has a capacity of 5.1 PB.

- `/ssd/p_gpuhack18_2` This is your team's `/ssd` space. This is a **Lustre**-mounted high performance parallel file system based on SSD disks. It is visible for read/write operations from all nodes across taurus. It is ultrafast and has a capacity of 43 TB. Please use it only for smaller data (1-10GB), that you need extremely often.

The team directories on `/scratch` and `/ssd` are set up in a way that all data stored there, is visible to the entire team. 

**IMPORTANT**: On Lustre based systems, it is not a good idea and create directories that have more than 1000-2000 files inside. If you happen to do that and each file is below 1MB, this will impact the performance of the entire file system and thus for all users of the cluster. Please do not create such directories.


## Transferring Data onto Taurus

Taurus has two specialized data transfer nodes. Both nodes are accessible via `taurusexport.hrsk.tu-dresden.de`. Currently, only `rsync`, `scp` and `sftp` to these nodes will work. A login via SSH is not possible as these nodes are dedicated to data transfers. 
External IP addresses (i.e. IP addresses not on the TU Dresden campus) can be enabled upon request. These requests should be send via eMail to `servicedesk@tu-dresden.de` and mention the IP address range (or node names), the desired protocol and the time frame that the firewall needs to be open. 

More details are available in the [taurus online docs on data exchange](https://doc.zih.tu-dresden.de/hpc-wiki/bin/view/Compendium/SystemTaurus#Transferring_Data_from_47to_Taurus).

# Using Jupyterlab

Taurus offers a [jupyter hub service](https://taurus.hrsk.tu-dresden.de/jupyter) that allows you to spawn jupyterlab on a taurus node. This service is very convenient, but only available on the TU Dresden campus. 

From the hackathon venue (i.e. on the TU Dresden campus), `taurus`'s jupyter service can be reached by connecting to the `eduroam` ESSID on the local wifi. When logged into the wifi, open a browser and open:

``` shell
https://taurus.hrsk.tu-dresden.de/jupyter
```

More documentation on how this service can be used is available [here](https://doc.zih.tu-dresden.de/hpc-wiki/bin/view/Compendium/JupyterHub).

[[back to root page]](../../README.md)
